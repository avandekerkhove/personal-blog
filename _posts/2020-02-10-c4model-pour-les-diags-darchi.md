---
layout: post
title:  "C4 model : LA solution pour les diagrammes d'archi ?"
description: "Modéliser facilement et efficacement les architectures logicielles avec le C4 model."
cover:  "/assets/c4model/architect.jpeg"
---

J'ai découvert il y a quelques temps le [C4 model](https://c4model.com/). 
C'est une méthodologie permettant de modéliser et documenter l'architecture logicielle d'un *système logiciel*.
Cela m'a tout de suite intrigué et pourra intéresser ceux qui connaissent un peu l'état de l'art sur ce sujet.

Il a été conçu par [Simon Brown](https://simonbrown.je/), notamment auteur du livre *Software Architecture for Developers*.

## Le souci avec les modélisations

On voit des diagrammes d'architecture dans tous les systèmes d'information. 
On reconnaît leur utilité. La plupart des équipes en font pour documenter leur solution. Les architectes s'en servent pour dessiner les interactions entre les systèmes.
Par contre, ils n'ont jamais le même formalisme...

### Trop de standards tue le standard

Tout le monde (?) apprend UML lors de son cursus informatique. C'est probablement l'outil le plus connu.   
Il y a ensuite le standard [Archimate](https://en.wikipedia.org/wiki/ArchiMate) qui offre des points de vue et notation différents. 
Evoquons également les modélisations qui permettent de concevoir les process métier type [BPMN](https://fr.wikipedia.org/wiki/Business_process_model_and_notation).
Mais ces différentes notations sont complexes, et souvent pas maitrisées. 
D'ailleurs UML est très riche mais on en utilise principalement les diagrammes de séquence, de classe ou de cas d'utilisation, ce qui est une petite partie du langage.

**En entreprise, on rencontre dans la plupart des cas des diagrammes qui sont simplement des assemblages de boîtes et de flèches... sans cohérence, sans notation unifiée.**

De plus, les outils permettant de modéliser sont également très hétérogènes.
Il y a les outils dédié à un standard, comme *[PlantUML](https://plantuml.com/)* par exemple.
Après on a les outils plus génériques comme *Visio*, *draw.io*, *Google Drawings*... 
On voit également des diagrammes faits dans *Powerpoint* ou équivalent. Tout ceci ne favorise pas vraiment le partage et la collaboration !

Enfin, la plupart des outils ne permettent pas de visualiser facilement différents niveaux d'abstraction selon le but recherché : vue du système, détail d'un composant...


### L'architecte, espèce en voie de mutation

Le travail d'architecte évolue dans les systèmes d'information. On voit de moins en moins d'architectes type *tour d'ivoire* avec une approche descendante.
Les diagrammes sont désormais souvent produits au sein même des équipes de développement.

<figure class="image">
  <img src="/assets/c4model/architecture_models.jpeg" />
  <figcaption><i>https://twitter.com/ghohpe/status/1171379436739944449</i></figcaption>
</figure>

Cela signifie qu'il faut disposer de notation et d'outils plus simples que ceux cités précédemment. Un développeur au sein d'une équipe a énormément d'autres choses à faire que des diagrammes !

### L'architecture modulaire ajoute de la complexité

J'ai déjà écrit à propos de l'essor de l'[architecture modulaire et des microservices]({{ site.baseurl }}{% post_url 2019-03-20-architecture-modulaire-microservices-on-en-est-ou %}). 
Cette architecture implique un découpage plus fin des composants du système d'information. 
Il est donc d'autant plus nécessaire de documenter ces composants et leurs interactions au sein du système.

_Finalement, on a besoin d'une solution **standardisée** et **simple**._
C'est là qu'intervient le C4 model !

## C4 ?

C4 est l'acronyme de **C**ontext, **C**ontainer, **C**omponent et **C**ode. 

L'élément central du C4 model est l'approche en **différents niveaux de zoom** de la représentation du système à modéliser. 
Ces vues permettent d'avoir des détails précis ou au contraire une vue globale. 


### Niveau 1 : Contexte

Le 1er niveau est un diagramme extrêmement simple, qui permet de visualiser le(s) application(s) à modéliser dans leur écosystème (ici le "Internet Banking System").
<figure class="image">
  <img src="/assets/c4model/context.png" style="width: 550px;" />
  <figcaption><i>Source : <a href="https://c4model.com">C4 model</a> de Simon Brown (licence <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a>)</i></figcaption>
</figure>

Les interactions avec les utilisateurs sont représentées. 
On voit également l'intégration du système dans l'existant, les échanges avec les autres briques. 
Ici il n'est pas question de technique.  
**Cette vue très macro peut être utilisée avec des personnes non techniques, par exemple des experts du domaine métier**.


### Niveau 2 : Conteneur

Bien sûr, le terme n'a aucun rapport avec les technologies comme Docker ou équivalent. 
Cette vue permet de visualiser les différentes briques logicielles qui composent le système modélisé : applications web et mobile, APIs HTTP, bases de données... 
Les technologies utilisées sont écrites, les interactions sont également plus précises en terme de protocole et format.

<figure class="image" style="margin: 0; cursor: pointer;" onclick="location.href='/assets/c4model/container.png'">
  <img src="/assets/c4model/container.png" />
  <figcaption><i>Source : <a href="https://c4model.com">C4 model</a> de Simon Brown (licence <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a>)</i></figcaption>
</figure>

<br/>C'est un niveau intéressant pour décrire le fonctionnement global d'une application. 
C'est idéal par exemple pour **expliquer le contexte à un nouveau développeur qui intègre l'équipe**. 
Ce diagramme sera particulièrement utile sur une architecture orientée microservices, car il fera apparaître les différents microservices et leurs relations.
Si des équipes différentes travaillent sur différentes briques d'un même système, ce schéma servira à montrer les frontières.

### Niveau 3 : Composant

Le 3ème niveau, "composant", décrit l'architecture locale d'une des briques logicielles. Le *conteneur* (voir niveau 2) est découpé sous la forme de multiples composants. 
Chaque composant représente une fonctionnalité du conteneur.
 
Dans cet exemple du site officiel, le conteneur représente l'API HTTP, elle a été découpée de la façon suivante :

<figure class="image" style="margin: 0; cursor: pointer;" onclick="location.href='/assets/c4model/component.png'">
  <img src="/assets/c4model/component.png" />
  <figcaption><i>Source : <a href="https://c4model.com">C4 model</a> de Simon Brown (licence <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a>)</i></figcaption>
</figure>

<br/>Ce niveau est clairement destiné à l'équipe qui développe le *conteneur* en question. 
On peut alors utiliser les différents composants comme base de découpage du code ! 
Ceci facilitera un découpage orienté sur le domaine métier plutôt que la technique, par exemple via l'adoption du [packaging by feature](http://www.javapractices.com/topic/TopicAction.do?Id=205).


### Niveau 4 : Code

Bon, ce niveau est optionnel, ce qu'indique même Simon Brown. 
Il descend au niveau du code, des interfaces et des classes. 
Il peut être implémenté par un diagramme de classes UML. 
Je ne m'étendrai pas sur ce niveau qui est à mon sens assez peu intéressant et qu'on peut générer à partir du code.


## Outillage et notation

Il n'y a pas de préconisation formelle sur la notation. Il y a en tout 5 éléments apparaissant sur les diagrammes : *Personne*, *Système*, *Conteneur*, *Composant* et *Relation*. 
A partir du moment où on sait modéliser ces éléments, on peut utiliser n'importe quel outil (y compris "à la main" sur tableau).

L'outil "officiel", développé par Simon Brown se nomme [Structurizr](https://structurizr.com/). 
C'est un outil en ligne, commercial et payant. Il offre une interface web permettant la construction des diagrammes. 
Il est également possible via des librairies client de décrire les diagrammes sous forme de code et de les exporter ensuite dans l'outil (par exemple [en java](https://github.com/structurizr/java/blob/master/docs/getting-started.md)).
D'autres fonctionnalités sont présentes comme la possibilité d'enregistrer les ADR ([architecture decision records](https://blog.engineering.publicissapient.fr/2019/03/05/architecture-et-documentation-les-adrs/)).

Des plugins ont été créés dans d'autres outils graphiques : par exemple [C4-PlantUML](https://github.com/RicardoNiepel/C4-PlantUML) ou [C4-draw.io](https://github.com/tobiashochguertel/c4-draw.io).

On peut aussi utiliser les librairies de *Structurizr* pour décrire notre système sous forme de code, puis l'exporter dans un autre format via un certain nombre d'[extensions](https://github.com/structurizr/java-extensions).
J'ai testé rapidement l'[extension Java pour générer un diagramme PlantUML](https://github.com/structurizr/java-extensions/blob/master/docs/plantuml.md) :


* 1- Utilisation de la librairie structurizr-plantuml, par exemple en Maven :

<pre><code class="xml">&lt;dependency&gt;
    &lt;groupId&gt;com.structurizr&lt;/groupId&gt;
    &lt;artifactId&gt;structurizr-plantuml&lt;/artifactId&gt;
    &lt;version&gt;1.3.1&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>

* 2- Définition des éléments et styles en Java, et génération du diagramme PlantUML

<pre><code class="java">Workspace workspace = new Workspace("Mon workspace", "Un workspace pour mon blog.");
Model model = workspace.getModel();

Person reader = model.addPerson("Lecteur", "Un lecteur de mon blog.");
Person writer = model.addPerson("Auteur", "Un auteur du blog (moi ;) ).");
SoftwareSystem softwareSystem = model.addSoftwareSystem("Blog perso", "Mon blog perso publié sur Github.");
reader.uses(softwareSystem, "Lit");
writer.uses(softwareSystem, "Publie");

ViewSet views = workspace.getViews();
SystemContextView contextView = views.createSystemContextView(softwareSystem, "SystemContext", "Diagramme de contexte (niveau 1) du système représentant mon blog perso.");
contextView.addAllSoftwareSystems();
contextView.addAllPeople();

Styles styles = views.getConfiguration().getStyles();
styles.addElementStyle(Tags.SOFTWARE_SYSTEM).background("#1168bd").color("#ffffff");
styles.addElementStyle(Tags.PERSON).background("#08427b").color("#ffffff").shape(Shape.Person);

StringWriter stringWriter = new StringWriter();
PlantUMLWriter plantUMLWriter = new PlantUMLWriter();
plantUMLWriter.addSkinParam("rectangleFontColor", "#ffffff");
plantUMLWriter.addSkinParam("rectangleStereotypeFontColor", "#ffffff");
plantUMLWriter.write(workspace, stringWriter);
System.out.println(stringWriter.toString());
</code></pre>

* 3- Intégration du diagramme généré dans PlantUML

<img src="/assets/c4model/blog_context.png" alt="Diagramme de contexte pour mon blog." style="border: 1px solid black;" />

Cette approche est intéressante car elle permet de stocker en tant que code les représentations d'architecture du système. 
Je m'interroge toutefois sur la maintenance et la lisiblité sur des systèmes complexes.

## En résumé

Le C4 model apparaît comme une solution simple pour modéliser une architecture logicielle. 
Les différents niveaux de détail de visualisation sont très bien pensés et permettent d'éviter de mettre trop de détails sur un diagramme. 
Ils servent également à communiquer avec des publics différents de façon claire. 
Les possibilités de générer et stocker les diagrammes *as code* sont intéressantes en vue d'utiliser *Git* comme source sur ces éléments.  
J'essaierai de le mettre en place sur un projet réél si j'en ai la possibilité.
