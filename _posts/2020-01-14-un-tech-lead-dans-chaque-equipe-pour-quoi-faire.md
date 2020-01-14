---
layout: post
title:  "Un(e) Tech lead dans chaque équipe, pour quoi faire ?"
description: "Pourquoi et comment faire emerger des equipes auto-organisees plutot que des techs leads."
cover:  "/assets/techlead/boats.jpg"
---

Une fois n'est pas coutume, ce billet est l'occasion d'exprimer une conviction plutôt qu'explorer un un sujet technique.
Depuis quelques années, les *tech leads* ont fleuri dans les équipes de développement des systèmes d'information. 
Ce qui me semblait au départ quelque chose de naturel a commencé à me questionner. 
Aujourd'hui j'en suis à me dire qu'on devrait remettre en question cette pratique de "Un tech lead par équipe".
Voici quelques éléments de réflexion...

## L'origine du rôle de tech lead ?

Quand le terme est-il né ? Quand j'ai commencé à travailler en 2006, on n'en parlait pas du tout.
Il faut dire qu'à l'époque, rester *développeur* était quelque chose d'assez peu valorisé en France, l'évolution "normale" consistait à aller vers la gestion de projet ! 

### Dans la théorie, point de tech lead

Est-ce que ce rôle a été théorisé via une méthode quelconque, un manifeste ou un livre ? Je n'ai rien trouvé faisant vraiment autorité.
J'ai donc fait l'exercice de le mettre en parallèle des méthodes que l'on utilise aujourd'hui.
Tout d'abord, sachant que la méthode *Scrum* est omniprésente, regardons les caractéristiques attribuées aux équipes de développement dans le [guide Scrum](https://scrumguides.org/scrum-guide.html#team-dev) : 
* *They are self-organizing*
* *Scrum recognizes no titles for Development Team members, regardless of the work being performed by the person*
* *Individual Development Team members may have specialized skills and areas of focus, but accountability belongs to the Development Team as a whole.*

Donc le rôle de tech lead n'est pas défini dans Scrum, il apparaît même comme une mauvaise pratique. 
Apparaît uniquement la notion de *servant leader* via le scrum master, qui est plutôt un facilitateur. 

<figure class="image">
  <img src="/assets/techlead/agile-teams.png" style="padding-top: 20px; padding-bottom:20px;" />
  <figcaption><i>https://www.visual-paradigm.com/scrum/what-is-self-organizing-team-in-scrum/</i></figcaption>
</figure>

N'étant pas un puriste de Scrum, j'ai relu le manifeste agile originel. 
Pour rappel, les [12 principes](http://agilemanifesto.org/iso/fr/principles.html) ont été érigés par des développeurs, comme des bonnes pratiques de développement de logiciels. 
Que dit le manifeste sur l'organisation des équipes ? <q>*Les meilleures architectures, spécifications et conceptions émergent d'équipes autoorganisées.*</q> 
Encore une fois on parle d'équipe "auto-organisée", ce qui va à l'encontre du principe d'un titre donné à un membre par l'organisation.

### Si à 50 ans, on est encore dév, on a quand même raté sa vie... &copy;

Depuis quelques années, avec l'essor des pratiques de *software craftsmanship*, l'explosion des demandes de profils techniques, les développeurs ont été de mieux en mieux considérés. 
Seulement, beaucoup n'ont pas envie de rester avec le même titre pendant de longues années.

Ces différents éléments me font penser que le rôle est parfois pensé comme une sorte de titre honorifique, donné à un développeur expérimenté, afin de lui montrer qu'il évolue et qu'il n'est pas au même rang qu'un junior.
Le premier souci que je vois, est que distinguer une personne des autres avec ce terme, induit un niveau hiérarchique. La traduction du terme "lead" entraîne des notions comme diriger, conduire, mener ! 
Et **distinguer un membre d'une équipe, revient à réduire l'importance accordée aux autres membres.**


## Mais en fait, c'est quoi le problème ?

Avant d'aller plus loin, je précise que je reconnais complètement le fait que certaines personnes ont des qualités de leadership, et l'exercent. C'est une bonne chose. 
Mais **le leadership doit émerger naturellement, pas se décréter**.
Je ne vois pas les bénéfices retirés, par contre les conséquences peuvent être assez néfastes.

Dans beaucoup de cas, tout va bien : le ou la tech lead est pédagogue, transmet des bonnes pratiques. 
Son expérience lui permet d'aider ses collègues moins expérimentés à progresser. Génial, le genre de personne hyper utile et qui permet vraiment à une équipe de travailler plus sereinement et efficacement !
En gros, on est dans l'exemple du "bon tech lead" tel qu'il est décrit assez régulièrement. 
Mais quel serait le résultat si cette personne n'a pas officiellement le titre de tech lead ? Elle ferait autrement son travail ? Certainement pas. 
Du coup, quels sont les bénéfices retirés ?
D'ailleurs, **plusieurs membres peuvent avoir ce profil de leader, c'est bien mieux pour l'équipe.**
Pourquoi distinguer un membre des autres ? Est-ce logique si un autre membre fait les mêmes tâches ? 
Sur ce sujet je rejoins complètement l'avis de Jean-Piere Lambert dans [cet article](https://jp-lambert.me/is-tech-lead-an-actual-role-on-the-team-7c040f2fd29b).


### Quand le tech lead est un micro-manager...

<img src="/assets/techlead/boss.jpg" width="500" />

Voici le cas vraiment problématique d'après moi. Dans certaines organisations le tech lead est une sorte de manager au sein des équipes. 
Voici le genre de responsabilités qui lui sont parfois confiées (ou qu'il s'attribue lui-même !) :
* choix techniques : langage, framework, outils, architecture...
* seul habilité à livrer en production
* seul avec les droits "admin" sur les outils (Git, bases de données, serveurs...)
* validation du code unilatérale
* présent seul à des points métier ou technique transverse

Dans ce cas là, les conséquences peuvent être désastreuses. **Les autres membres de l'équipe sont déresponsabilisés**. 
On ne peut pas attendre d'un développeur qu'il s'implique et soit autonome, si on ne lui donne pas les pouvoirs.
Le tech lead va assumer la qualité globale alors que cela doit être la responsabilité de l'équipe. 

Ensuite, le **[bus factor](https://savoiragile.com/2015/03/19/mefiez-vous-des-bus/) de l'équipe = 1**, ce qui est très mauvais. Oui, si la personne qui assume ce rôle est absente du jour au lendemain, l'équipe est figée !
S'il faut la remplacer, cela peut être très compliqué de trouver la bonne personne.

Enfin, si dans ces conditions le rôle est assumé par quelqu'un qui n'a pas les compétences nécessaires, on peut tout de suite mettre l'application à la poubelle !
En effet avec tant de responsabilités, **le rôle devient critique**.


## Du coup on fait quoi, concrètement ?

D'après beaucoup de gens (dont je fais partie), c'est la **responsabilité collective** de l'équipe qui est engagée sur tous les domaines : qualité du logiciel livré, qualité du code, bugs, temps de réponse, délais de développement...

Voici quelques pistes pour favoriser l'auto-organisation, faire émerger des leaders plutôt que les imposer.


### Un tech lead unique non, plusieurs leaders oui

L'idéal est donc de **ne pas attribuer un titre particulier à un membre d'une équipe de développement**. Mais l'équipe doit réussir à s'organiser.
Beaucoup de choses se jouent lors du recrutement et de la composition de l'équipe.
Il faut créer des équipes **matures**, avec une bonne proportion de développeurs expérimentés.

Et n'hésitons pas à changer les termes des fiches de poste ou de mission. Plutôt que des tech lead, chercher des **mentors**. 
En compétences, insister sur les notions comme le coaching, le mentorat, la pédagogie. 
Mettre l'accent sur les *soft skills* : écoute, empathie, ouverture d'esprit, humilité...
Les membres de l'équipe doivent participer au recrutement d'un nouveau membre.
Tout ceci favorise la mise en place d'une équipe auto-organisée avec une communication fluide.


### Communications entre l'équipe et l'extérieur

La communication qui entre/sort de l'équipe doit être **transparente**.
Il est important que l'équipe dispose d'au moins un **canal global de communication**, et surtout il faut imposer son utilisation. Cela peut être un chanel slack, un alias mail unique, ...
Chaque membre de l'équipe a accès à toutes les communications qui concernent les sujets de l'équipe. 
Ceci permet d'éviter que la hiérarchie ou une personne du métier s'adresse en direct à un membre en particulier.

De la même façon, les réunions doivent concerner systématiquement plusieurs membres.  
**Lors des points importants avec les experts métier, toute l'équipe doit être présente.**
Pour les autres points, essayer d'impliquer des membres différents à chaque fois.

Quand il s'agit de faire des démos à d'autres équipes par exemple, on peut faire en sorte que chacun puisse participer, ou le faire à tour de rôle. 
Cela renverra en plus une image positive de l'équipe à l'extérieur.


### Partager les pouvoirs !

**Tous les membres de l'équipe doivent avoir les mêmes droits**. Notamment de déployer et débugger en production, d'accéder aux bases de données, au code.
Il faut faire confiance aux personnes dans les équipes. Un développeur n'est pas un être malveillant qui serait là pour faire planter son application en prod... c'est un professionnel qui cherche généralement à bien faire son travail.
Et si les process de build, validation et déploiement sont correctement établis, le risque de faire une grosse boulette est très limité. Après, qui n'a jamais fait d'erreur ?


### Choix techniques

**Les choix techniques, notamment les détails d'implémentation, doivent être pris collectivement**. 
Il est évident que les plus experts ou expérimentés orientent davantage les décisions, mais pas parce qu'ils sont "chefs" : parce qu'ils sont capables de choisir, d'expliquer et de convaincre. 
Quand on n'arrive pas à se mettre d'accord, un consensus peut être trouvé dans la majorité des cas, ou on peut chercher une médiation externe.



## En résumé

Je vois aujourd'hui l'attribution du rôle de tech lead comme quelque chose d'inutile.
Au mieux, la posture est bien tenue, et le résultat est le même qu'au sein d'une équipe auto-organisée avec les mêmes personnes.
Par contre, si le tech lead est un micro-manager, cela déresponsabilise les autres et peut avoir des répercussions importantes.
Il faut plutôt créer les conditions pour faire émerger tous les leaders potentiels. 
Pour cela on s'appuiera sur le recrutement, et la mise en place d'un partage maximum des actions et pouvoirs entre les membres.

{% include page_divider.html %}
<small>crédit photo Miguel Á. Padriñán</small>
