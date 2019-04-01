---
layout: post
title:  "Découverte du framework Micronaut"
date:   2019-01-02
categories: 
cover:  "/assets/micronaut/logo.png"
---
##  Un nouveau framework de développement d'applications JVM

Que dit le [pitch][2] ? "*A modern, JVM-based, full-stack framework for building modular, easily testable microservice and serverless applications.*" Hé oui, rien que ça. A priori ça sert donc à développer des applications et les exécuter sur une JVM, et que c'est orienté sur les **microservices** et le **serverless**. La version 1.0 a été releasée en Octobre dernier. C'est l'occasion de faire un essai !

##  La genèse de Micronaut

Le constat fait par l'équipe de développement est que depuis quelques années, énormément de choses ont changé dans le développement des applications. Aujourd'hui, on fait des micro-services, sous forme de containers ; on veut pouvoir *scaler* facilement, exposer des API consommées par plusieurs types de périphériques, avec un niveau de performance élevé. Les anciens frameworks JVM tels que Spring ou Grails n'ont pas été construits avec ces problématiques.  
**Le but affiché par les créateurs est de créer un framework de développement léger, rapide à démarrer, peu consommateur de mémoire et modulaire !** Clairement, on veut concurrencer Spring Boot en proposant une expérience de développement similaire, mais en s'affranchissant de la lourdeur de ce dernier. L'équipe de développement est la même que celle derrière le framework Grails.

##  Comment ça marche - *Runtime vs Compile time*

Un framework tel que Spring est apprécié par beaucoup de développeurs pour la productivité qu'il apporte, même si la courbe d'apprentissage est raide. Une fois pris en main, il est simple d'exposer et sécuriser une API, d'accéder à de multiples bases de données, d'émettre ou consommer des messages, etc. Le souci est que pour permettre tout cela, il fait énormément de choses au démarrage de l'application : scan du code, injection de dépendances, activation de profils... tout cela est fait au **runtime**. Cela a pour conséquence que plus un projet Spring est important, plus le temps de démarrage et la consommation mémoire sont élevés.

**Micronaut prend le parti de tout faire à la compilation : il n'utilise pas la réflexion (introspection dynamique des classes)**. Il utilise la compilation ahead-of-time (AOT) afin de gérer tout cela lors du *build*. Cependant, le framework propose le même genre de facilités aux développeurs que Spring et permet donc une grande productivité. Il promet donc le meilleur des deux mondes !

## On va développer quoi avec Micronaut ?

On peut l'utiliser pour construire des API REST, clients d'API, services de traitement de données, messaging, ... Micronaut propose un **modèle asynchrone et non bloquant** et le rend donc indiqué pour développer des applications *réactives*. Pour cela la couche réseau est basée sur le serveur [Netty][3] qui apporte la gestion de l'*event loop*. C'est le serveur utilisé également par les frameworks Vert.x et Spring Webflux par exemple (version réactive de Spring MVC). **Le framework supporte les langages Java, Kotlin et Groovy**. Voici quelques fonctionnalités en vrac :

*   service discovery (Consul, Eureka, ...)
*   gestion des paramètres de configuration externe (Consul, Amazon ParameterStore)
*   support du serverless (AWS Lambda, OpenFaas)
*   Interaction avec les bases mongoDB, Neo4j, Postgres, Cassandra, Redis...
*   Support d'Apache Kafka
*   Mécaniques intégrées de *retry* et *circuit breaker*
*   ...

##  Allez, jouons avec !

Micronaut s'installe très facilement grâce à [SDKMAN][4] !

<pre><code class="bash">$ sdk install micronaut</code></pre>

###  Pour démarrer, le CLI

Micronaut met à disposition un CLI (*Command-line interface*) efficace. Pour démarrer un squelette d'application :

<pre><code class="bash">$ mn create-app com.mycompany.mygreatservice</code></pre>

Cela créée une structure, une classe de démarrage, un Dockerfile, la configuration des dépendances... Le langage est Java par défaut et les dépendances gérées par Gradle, mais il est possible d'utiliser maven avec l'option *-b maven*. Il y a un certain nombre de *features* qui permettent de pré-configurer une fonctionnalité en ajoutant les dépendances et le squelette de configuration nécessaire, par exemple :

<pre><code class="bash">$ mn create-app com.mycompany.mygreatservice --features discovery-consul, mongo-reactive</code></pre>

Malheureusement il n'est pas possible d'ajouter une *feature* une fois que le projet a été créé. Création d'un *controller* :

<pre><code class="bash">$ cd mygreatservice/ 
$ mn create-controller com.mycompany.cars 
</code></pre>

Une classe *com.mycompany.CarsController* est créée avec une route */cars* par défaut, ainsi que la classe de tests associée. Du coup, on peut tester :

<pre><code class="bash">$ ./gradlew run 
$ curl -i http://localhost:8080/cars

HTTP/1.1 200 OK
Date: Wed, 2 Jan 2019 23:50:42 GMT
connection: keep-alive
transfer-encoding: chunked
</code></pre>

Regardons un peu le code généré ! Voici la classe de démarrage de l'application :

<pre><code class="java">public class Application {

    public static void main(String[] args) {
        Micronaut.run(Application.class);
    }
    
}
</code></pre>

Et le controller :

<pre><code class="java">@Controller("/cars") 
public class CarsController {

    @Get("/")
    public HttpStatus index() {
        return HttpStatus.OK;
    }
    
}</code></pre>

On remarque déjà des similitudes avec le modèle de développement Spring. Il est à noter que le framework fait des choix modernes par défaut : par exemple, les routes consomment et produisent du JSON par défaut, pas besoin de préciser le *content-type* dans ce cas.

###  Adoption du modèle réactif

Nous avons évoqué le fait que Micronaut utilise Netty pour la couche réseau. Pour garantir l'exécution en mode non bloquant, il faut utiliser les types fournis par une librairie implémentant la spécification [Reactive Streams][5], comme par exemple [Reactor][6] ou [RxJava][7].

Prenons l'exemple d'un endpoint classique de sauvegarde. On va écrire quelque chose comme :

<pre><code class="java">@Post 
public HttpResponse&lt;Customer&gt; save(@Body Customer customer) { 
    return service.insertInDatabase(customer); 
}
</code></pre>

Dans ce cas, Micronaut utilise un thread pool classique. L'exécution est bloquante : le code du service n'est exécuté que lorsque l'objet est reçu en totalité. La réponse n'est envoyée que lorsque le service a fini son exécution. Entre ces étapes, le thread courant est bloqué, en attente. Si on a une latence réseau importante les resources du serveur ne sont pas exploitées au mieux. Voici le même exemple en utilisant RxJava :

<pre><code class="java">@Post 
public Single&lt;Httpresponse &lt;Customer&gt;&gt; save(@Body Single&lt;Customer&gt; customer) {
    return customer.map(c -> { 
       service.insertInDatabase(c); 
       return HttpResponse.created(c); 
     }); 
}
</code></pre>

**Ici la requête est non bloquante et le modèle *event-loop* de Netty est utilisé. Entre les différentes étapes, le thread est capable d'exécuter d'autres requêtes plutôt que d'être bloqué.**

Si on veut être réactif de bout en bout, il est préférable d'utiliser également une librairie réactive quand il s'agit d'accéder aux données. Ce n'est pas toujours possible. JDBC est une API bloquante par exemple. Dans ce cas l'exécution des accès à la base de données sera basculée dans le thread pool "bloquant" et on reviendra en mode non bloquant ensuite.

###  Accès aux données avec mongoDB réactif

Du coup on opte pour le driver réactif pour MongoDB. Pour l'activer, on ajoute la dépendance dans le fichier de dépendances, ici dans *build.gradle* :

    compile "io.micronaut.configuration:micronaut-mongo-reactive"
    

Et il faut déclarer le chemin vers la base dans le fichier *application.yml*

    mongodb:
      uri: mongodb://localhost:27017
    

Avec cette configuration, on dispose d'un objet *MongoClient* qui peut être utilisé pour interagir avec la base de données. Cet objet peut être injecté à la manière d'un bean Spring. On peut ensuite requêter dans la base de données de manière réactive :

<pre><code class="java">private MongoCollection&lt;Customer&gt; getMongoCollection() {
    return mongoClient
            .getDatabase("my-great-database")
            .getCollection("myGreatCollection", Customer.class);
}

public Maybe&lt;Customer&gt; getByLogin(String login) {
    return Flowable.fromPublisher(
            getMongoCollection().find(Filters.eq("login", login)).limit(1))
                .firstElement();
}
</code></pre>

### Service discovery avec Consul

Dans un environnement micro-services il est très utile de pouvoir faire du *service discovery* : chaque service va s'enregistrer au sein de l'éco-système, ce qui permettra aux autres services de l'appeler sans connaître son adresse réelle. [Consul][8] permet de faire cela et son intégration est facile dans Micronaut. Il faut ici encore ajouter une dépendance dans le *build.gradle* :

    compile "io.micronaut:micronaut-discovery-client"
    

Puis on ajoute quelques paramètres dans le fichier *application.yml* pour activer l'enregistrement du service.

    consul:
      client:
        registration:
          enabled: true
        defaultZone: "localhost:8500"
    

On constate l'enregistrement automatique du service dans consul lorsqu'il est démarré : <img src="/assets/micronaut/consul.png" alt="consul services">


Il est alors possible de simplifier grandement l'appel à un service qui expose une API REST. Si on veut appeler un tel service dans une autre application, il suffit de déclarer une interface annotée *@Client* avec l'identifiant du service, et les méthodes correspondant aux endpoints de l'API.

<pre><code class="java">@Client("customer")
public interface CustomerServiceClient {

    @Get("/customers/{login}")
    public Single&lt;Customer&gt; getByLogin(String login);
    
}
</code></pre>

On peut ensuite appeler directement ce client dans notre code.

<pre><code class="java">public class MySecondService {

    private final CustomerServiceClient customerClient;
    
    public MySecondService(CustomerServiceClient customerClient) {
        this.customerClient = customerClient;
    }

    public void callMyCustomerService(String login) {
        customerClient.getByLogin(login)
                .map(customer -> {
                    // do what you want here
                });
    }
}
</code></pre>

###  Et donc, les promesses de performance et de légéreté ?

Prenons un exemple d'application avec les features ci-dessus actives et développées : consul service discovery et MongoDB. Voir la taille du *fat jar* compilé :

<pre><code class="bash">$ du -h ./customer/build/libs/customer-0.1-all.jar 
14M ./customer/build/libs/customer-0.1-all.jar
</code></pre>

L'exécutable fait une taille plutôt raisonnable de 14 Mo. Démarrons l'application pour constater le temps de démarrage :

<pre><code class="bash">$ java -jar ./customer/build/libs/customer-0.1-all.jar
12:02:55.340 [main] INFO  io.micronaut.runtime.Micronaut - Startup completed in 1169ms. Server Running: http://localhost:8080
12:02:55.446 [nioEventLoopGroup-1-3] INFO  i.m.d.registration.AutoRegistration - Registered service [customer] with Consul
</code></pre>

J'ai constaté un temps similaire sur plusieurs essais. Bien sûr ça n'a rien d'un *benchmark* dans les règles de l'art mais c'est plutôt encourageant.

Concernant l'empreinte mémoire, j'ai pu effectuer quelques tests. Sur une application de type serveur HTTP avec un seul endpoint, j'ai constaté une consommation mémoire d'environ 100Mo. Il est notable de constater que l'application fonctionne si on la lance avec une taille maximale de *heap* très petite (10Mo). Malheureusement c'est trop peu significatif sur une petite application et je n'ai pas eu d'effet "waouh". Pour tirer des conclusions pertinentes et constater la plus-value réelle de Micronaut, il faudrait faire un benchmark complet sur une application plus proche d'une application de production, et comparer avec d'autres frameworks. Peut-être un sujet pour un prochain article ;)

Vous trouverez un exemple complet avec 2 services sur mon [github][10] !

##  En conclusion

Micronaut est un nouveau framework JVM qui est vraiment prometteur. Il est simple à utiliser, rapide, la documentation est claire et concise. Le projet est très actif au niveau des contributions.

L'enjeu pour l'équipe de développement sera de garder le côté "micro" tout en augmentant le périmètre des fonctionnalités. Je pense qu'il peut se positionner comme une alternative crédible sur ce marché. En tout cas, je vais surveiller son évolution avec intérêt !

<br />
<span class="page-divider"><span class="one"></span><span class="two"></span></span>
*<small>Publié originellement sur le site de [Salto Consulting](http://www.salto-consulting.com/decouverte-du-framework-micronaut/) le 2 Janvier 2019.</small>* 


 [2]: http://micronaut.io/
 [3]: https://netty.io/
 [4]: https://sdkman.io/
 [5]: http://www.reactive-streams.org/
 [6]: https://projectreactor.io/
 [7]: https://github.com/ReactiveX/RxJava
 [8]: https://www.consul.io/
 [10]: https://github.com/avandekerkhove/micronaut-poc