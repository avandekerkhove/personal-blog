---
layout: post
title:  "RSocket, le protocole réactif"
description: "Aperçu de RSocket, protocole d'échanges de messages inter-applications. Exemples d'implémentation en Java."
date:   2019-01-28
categories: 
---
![RSocket logo](/assets/rsocket/rsocket-pink.png)

[RSocket][2] (pour *Reactive* Socket) est un nouveau protocole de communication. Il spécifie des façons d'échanger des messages au format binaire entre applications. C'est un protocole de niveau **applicatif** qui permet des communications correspondant aux besoins modernes : **push de données, échanges bi-directionnels, reprise de connexion, asynchronisme...**

Il est conçu pour être utilisé autant pour de la communication de serveur à serveur, que serveur à périphérique (smartphone, navigateur web etc.).

Le [protocole][3] est open-source. Créé au départ par Netflix, il est désormais supporté par Facebook, Pivotal et Netifi. Il doit être intégré prochainement dans le framework Spring (cf *[issue][4]*). La spécification du protocole est actuellement en version 0.2 mais la release est proche, cette version étant considérée comme une *release candidate* 1.0. Rentrons un peu dans le détail !

##  Les points clé de RSocket

### Reactive streams - le contrôle des flux

La conception de RSocket s'appuie sur le [manifeste réactif][5] et la spécification [Reactive Streams][6]. Il s'agit d'implémenter des systèmes asynchrones et non bloquants, mais pas uniquement.

Un des apports fondamentaux apporté par le dogme réactif est la **backpressure**. Ce paradigme permet à un consommateur de données dans un système, d'informer les autres applications qu'il est surchargé. Les producteurs de cette donnée doivent assimiler cela et ne pas surcharger le flux. Le consommateur peut également choisir de consommer les données au rythme qu'il veut. Le but est de produire un sytème **résilient** sans devoir implémenter des mécanismes complexes de type *circuit breaker*.

RSocket introduit ces éléments dans sa spécification, le rendant indiqué pour implémenter des applications réactives.

### Autres caractéristiques

*   RSocket **est indépendant du transport** sous-jacent. Actuellement il peut fonctionner avec TCP, WebSockets, Aeron, ou HTTP/2. Typiquement on choisira TCP pour des échanges de serveur à serveur, et Websocket pour navigateur à serveur.
*   **Reprise de connexion**. Si une connexion est coupée entre les 2 participants, cela peut être problématique pour les communications de type "longue durée" (abonnement à un flux de données). Le protocole fournit les moyens de reprendre la discussion au même endroit dans une nouvelle connexion, grâce à une notion de *position implicite*. 
*   Rsocket est un **protocole de haut niveau**. Le but recherché par les créateurs est de fournir des impémentations directement utilisables au sein des applications. Ces librairies sont disponibles dans différents langages de programmation. 
*   Les échanges sont au **format binaire**, afin de maximiser les performances et d'optimiser les resources. Cela peut rendre plus difficile le debug des messages. Cependant, c'est totalement cohérent quand on pense que l'immense majorité des échanges se font entre 2 machines et ne sont pas lus par un humain. Les applications devront donc implémenter la sérialisation et désérialisation de leur format natif vers du binaire.

##  Modes d'interaction

La base du protocole tient dans les différents modes d'interaction proposés. RSocket fournit 4 modes distincts :

1.  **Fire-and-Forget** : requête unique, pas de réponse envoyée par le serveur 
2.  **Request/Response** : "HTTP-like" : 1 requête, 1 réponse. 
3.  **Request/Stream** : requête simple, réponse multiple. Le serveur renvoie une collection (*stream*) en réponse. Ce n'est pas une liste figée mais bien un flux qui arrive au fil de l'eau. 
4.  **Channel** : échanges bi-directionnels. Les 2 participants s'envoient des flux de messages l'un à l'autre.

Ces modes d'interaction ont été pensés pour répondre aux besoins actuels des applications. Ainsi, le *push* de données est supporté par le mode *request/stream*. Cela permet par exemple, de gérer un flux d'informations à recevoir continuellement, sans avoir besoin de requêter plusieurs fois le serveur. Le mode *fire-and-forget*, avec sa requête unique sans réponse, permet d'optimiser dans des cas où la réponse peut être ignorée. Le mode *channel* implémente un dialogue complet entre deux composants.

Ces différents modes ainsi que les points clés listés ci-dessus sont le socle de RSocket.

##  Les implémentations

A ce jour le protocole a des **implémentations en Java, Javascript, C++ et Kotlin**. Voyons un peu comment cela marche en Java dans la section suivante.

### Exemples en java

L'implémentation en Java est basée sur la librairie [Reactor][7]. Au niveau du transport nous allons utiliser ici le transport TCP via le framework [Netty][8]. Les 2 dépendances suivantes sont suffisantes pour commencer à implémenter RSocket dans une application : **io.rsocket:rsocket-core** et **io.rsocket:rsocket-transport-netty**.

Démarrons un serveur en local :

<pre><code class="java">RSocketFactory.receive()
    .acceptor((setup, socket) -> Mono.just(new AbstractRSocket() {})) // ne fait rien
    .transport(TcpServerTransport.create("localhost", 7000))
    .start()
    .subscribe();
</code></pre>

Ce serveur ne va rien faire car on n'a pas spécifié de comportement concret sur la méthode *acceptor*. Il faut fournir une implémentation des interfaces *SocketAcceptor* et *RSocket* afin de déterminer ce que fait le serveur quand il reçoit un message. Il est intéressant de regarder l'interface *RSocket* pour constater qu'elle demande l'implémentation des 4 modes d'interaction évoqués plus haut :

<pre><code class="java">public interface RSocket extends Availability, Closeable {
  // [...]
  Mono&lt;Void&gt; fireAndForget(Payload payload);
  Mono&lt;Payload&gt; requestResponse(Payload payload);
  Flux&lt;Payload&gt; requestStream(Payload payload);
  Flux&lt;Payload&gt; requestChannel(Publisher&lt;Payload&gt; payloads);
  // [...]
}
</code></pre>

Prenons l'exemple d'un service de streaming de "news". Lorsque le serveur reçoit une requête d'un client, il va envoyer un flux continu d'actualités qui se met à jour sans nouvelle requête. Nous allons devoir implémenter la méthode *requestStream* pour gérer cette interaction. La classe *Payload* est la classe qui représente un message binaire qui transite sur la connexion ; il faut donc effectuer les transformations entre les objets métier et ce type. Voici donc à quoi peut ressembler une implémentation du flux côté serveur :

<pre><code class="java">SocketAcceptor socketAcceptor = (setup, sendingSocket) -> {
    return Mono.just(new AbstractRSocket() {
        @Override
        public Flux&lt;Payload&gt; requestStream(Payload payload) {
            return newsProducer.streamNews(payload) // service métier qui fournit le flux de données en fonction de la requête
                    .map(NewsMapper::toByte)        // sérialisation de l'objet métier
                    .map(DefaultPayload::create)    // creation du payload (methode fournie par l'implémentation rsocket-java)
                    ;
        }
    });
};
</code></pre>

Sur le même exemple, créons la socket et utilisons là pour que le client puisse interroger le serveur et récupérer les news :

<pre><code class="java">RSocket clientSocket = RSocketFactory.connect()
        .transport(TcpClientTransport.create("localhost", 7000))
        .start()
        .block();
        
clientSocket
    .requestStream(DefaultPayload.create("Donne moi les news s'il te plait"))
    .map(Payload::getData)            
    .map(NewsMapper::fromByteBuffer)  // désérialisation du message vers l'objet métier
    .doOnNext(newsConsumer::readNews) // appel du service métier de lecture des news reçues
    .doFinally(signalType -> clientSocket.dispose())
    .then()
    .block();
</code></pre>

Ces quelques exemples démontrent que l'on peut utiliser RSocket dans une application Java très simplement. Cela nécessite en amont l'adoption de la programmation réactive.

### Netifi proteus

[Proteus][9] est une plateforme basée sur RSocket. Elle fournit un *broker* auquel les applications vont se connecter, le broker se chargeant des échanges entre les applications. Il gère le routage entre les services, la sécurité, le load balancing. Une console web permet de visualiser et d'administrer la plateforme. Comme souvent, on dispose d'une version communautaire open-source avec les fonctionnalités de base, et la version *enterprise* contient des fonctionnalités avancées (connecteurs, métriques, alerting, ...)

Les échanges sont encodés à l'aide de [Protobuf][10]. Ceci permet de spécifier les contrat d'échanges : mode d'interaction, types d'entrée/sortie, etc. Les interfaces client/serveur sont ensuite générées, et il suffit de les implémenter pour écrire notre logique métier. Proteus se charge de la sérialisation des objets et de la communication via RSocket.

Afin d'illustrer Proteus, voici quelques copies d'écran de la console web.

*Gestion des brokers :* 
![console web proteus](/assets/rsocket/proteus_broker.png)


*Statut des services connectés :* 
![console web proteus](/assets/rsocket/proteus_services.png)

L'outil est intéressant mais semble encore un peu limité. Il est par exemple impossible d'envoyer un message à partir de la console, ce qui serait très pratique en développement. Je l'ai trouvé également assez lourd à démarrer via docker, sachant que c'est uniquement un "passe-plat" et qu'il ne stocke pas les messages. Les librairies sont pour l'instant disponibles pour Java, Javascript et Spring Framework mais d'autres devraient arriver prochainement. A suivre donc !

## Pour conclure

Dans des systèmes de plus en plus distribués et découplés, les échanges de message asynchrones deviennent un standard de communication entre les applications. RSocket s'inscrit dans cette logique mais se démarque en apportant les principes réactifs au niveau du protocole de communication.

Supporté par des grandes entreprises du numérique, son adoption en sera peut-être facilitée. A suivre...

{% include page_divider.html %}
*<small>Publié originellement sur le site de [Salto Consulting](http://www.salto-consulting.com/rsocket-le-protocole-reactif/) le 28 Janvier 2019.</small>*

 [2]: http://rsocket.io/
 [3]: https://github.com/rsocket/rsocket/blob/master/Protocol.md
 [4]: https://github.com/spring-projects/spring-framework/issues/21292
 [5]: https://www.reactivemanifesto.org/
 [6]: http://www.reactive-streams.org/
 [7]: https://projectreactor.io/
 [8]: https://netty.io/
 [9]: https://www.netifi.com/proteus
 [10]: https://developers.google.com/protocol-buffers/
 