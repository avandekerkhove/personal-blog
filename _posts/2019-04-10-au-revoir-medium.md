---
layout: post
title:  "Au revoir Medium, bonjour Jekyll et GitHub Pages"
description: "Pourquoi j'ai quitté la plateforme Medium pour mon blog et adopté Jekyll et Github Pages."
cover:  "/assets/bye/plane.jpeg"
---

J'ai décidé ~~d'essayer~~ de blogger régulièrement depuis le début de l'année. 
Outre le site de mon entreprise où j'ai l'ocassion d'écrire des articles techniques, je voulais un espace personnel avec moins de contraintes (et qui n'engage que moi).
Comme beaucoup de monde j'ai commencé à écrire des *stories* sur Medium, mais j'ai décidé de changer. Voilà pourquoi.

## Medium, ça a l'air bien

De prime abord, Medium est un excellent choix pour se lancer dans l'écriture. **La plateforme est super simple**. 
En quelques minutes, on peut commencer à écrire quelque chose. L'éditeur ne demande quasiment aucun temps d'adaptation. 
Alors bien sûr, les options sont minimalistes : titres, séparateurs de paragraphes, images/vidéos, citations, inserts, ... 
Les basiques sont là mais on ne peut pas faire de folie niveau design.

Et avec cette facilité, on a un style élégant, simple, épuré. **L'expérience de lecture est très bonne et sur tous les supports**.


## Medium, ça a ses limites

### Tu vas créer un compte, oui ou non ?!

C'est le premier élément qui ternit l'expérience de lecture. Si on visite le site sans être enregistré, on est 
harcelé d'incitations à créer un compte. Voici quelques popups apparues en à peine 2 minutes de navigation : 

![medium home page](/assets/bye/medium_account1.png)

![medium home page](/assets/bye/medium_account2.png)

![medium home page](/assets/bye/medium_account3.png)

*Pardon the interruption*... Ne vous excusez pas, laissez moi lire tranquille !  
Bon, je trouve compréhensible que leur souhait soit qu'on crée un compte. Mais cela exclut automatiquement une partie de lecteurs potentiels.


### Bon c'est pas tout ça, on va essayer de faire de l'argent

Medium a un programme de *membership*. Pour 5$ par mois on est membre *premium* de la plateforme. 
Cela permet notamment d'avoir accès en illimité à tous les articles. En effet certains articles sont taggés premium, et 
sans payer on ne peut en lire que quelques uns par mois. Le souci, c'est que quand j'ouvre la page d'accueil, je vois ça :

![medium home page](/assets/bye/medium_home.jpg)

La plus grande partie de la page d'accueil ne contient que des articles premium. Quand on descend dans la page, on a encore une grande quantité d'articles premium. 
Dans les mails quotidiens reçus de Medium, on ne me propose quasiment que des articles premium. 

Cette situation empire de plus en plus. **Désormais, si on ne paie pas, on perd l'accès à énormément de contenu**.


### Le contenu n'est pas le vôtre !

C'est probablement le plus gros souci quand on veut utiliser son blog comme une aide commerciale. 
Quand on lit un article sur medium, on lit "sur medium", pas ailleurs. Si on en parle entre collègues, on va évoquer Medium, rarement le nom de l'auteur ou la société. 
Depuis fin 2017, **il n'est plus possible d'avoir un nom de domaine dédié sur Medium**. 

Il est vrai que Medium permet de gagner en visibilité grâce à l'aspect social (claps, followers...). 
Mais on n'aura que peu de répercussions positives, en terme de notoriété, de récupération du traffic. Globalement on travaille 
autant pour la notoriété de Medium que pour soi-même...

*Bref, la politique récente de Medium concernant les contenus et la monétisation rend cet outil moins intéressant qu'avant. Pour quelqu'un qui veut blogger régulièrement, c'est à réfléchir.*

## Cherchons une solution alternative

Du coup j'ai commencé à chercher une autre solution pour créer un blog. Il y a bien sûr le grand classique *WordPress* ou les équivalents. 
Mais je cherchais quelquechose de très simple, très léger. Et là je me suis souvenu d'une discussion avec un collègue et ami (il se reconnaîtra) qui m'avait parlé de [Jekyll](https://jekyllrb.com/).


### C'est quoi Jekyll ?

Jekyll est un **générateur de site statique** écrit en Ruby. Cela signifie que le logiciel est capable de traiter un ensemble de fichiers et avec ceux-ci, de générer tous les fichiers nécessaires 
à l'affichage d'un site internet : fichiers HTML, Javascript, CSS. Par rapport à un CMS, il n'y a aucun dynamisme, pas de base de données. 
Le site peut donc être uploadé sur un serveur très facilement car on n'a pas besoin d'un *runtime* spécifique (PHP...). Et évidemment, comme le site est statique, c'est très rapide !


### Comment ça marche ?

#### Layouts et pages
Les **layouts** décrivent la structure de chaque type de page. On commence par écrire un layout par défaut appelé... *default.html* (!), 
histoire de définir le squelette. Voici par exemple le contenu du layout par défaut de mon blog :

{% highlight html %}
<!DOCTYPE html>
<html>
  {% raw %}{% include head.html %}{% endraw %}
  <body>
    {% raw %}{% include header.html %}{% endraw %}
    <div class="page-content">
        {% raw %}{{ content }}{% endraw %}
    </div>
    {% raw %}{% include footer.html %}{% endraw %}
  </body>
</html>
{% endhighlight %}

Ensuite, on définit des layouts qui héritent de celui par défaut, comme *post.html* pour les articles. 
Pour cela on écrit une entête nommée *front matter* qui permet de spécifier le layout étendu. 
La variable *content* est utilisée pour injecter le contenu spécifique de la page.
Voici un exemple basique de layout *post.html* :

{% highlight html %}
---
layout: default
---
{% raw %}<h1>{{ page.title }}</h1>
<p>{{ page.date }} - Ecrit par {{ page.author }}</p>
{{ content }}{% endraw %}
{% endhighlight %}

Maintenant qu'on a un layout, on peut écrire une page avec ! C'est très simple. 
Chaque page est un fichier qui peut être au format HTML ou **Markdown**. Nous pouvons positionner la valeur de variables dans l'entête, comme le titre de l'article.  
Ecrivons notre premier article :

{% highlight html %}
---
layout: post
title:  "Au revoir Medium, bonjour Jekyll et GitHub Pages"
author: Alex
---
Ceci est l'intro de mon article.

## Ceci est un titre de niveau 2
bla bla...
{% endhighlight %}

Pour prévisualiser en local, il suffit de lancer la commande suivante :
{% highlight bash %}
bundle exec jekyll serve
{% endhighlight %}
==> Le site est visualisable sur *http://localhost:4000* !


#### Templating
Jekyll utilise [Liquid](https://shopify.github.io/liquid/) comme moteur de templating.
Il est possible d'utiliser des opérateurs comme *for*, *if*, *and*... Utilisé avec les variables disponibles et d'autres personnalisées, 
on peut s'en servir pour configurer certaines parties du code HTML :
{% highlight html %}
{% raw %}<title>{% if page.title %}{{ page.title }}{% else %}{{ site.title }}{% endif %}</title>{% endraw %}
{% endhighlight %}

#### Thèmes
Pour le thème, si on est comme moi pas doué en CSS/Javascript, on peut récupérer des thèmes libres ou en acheter. 
Les sites [jekyllthemes.org](http://jekyllthemes.org/) et [jekyllthemes.io](https://jekyllthemes.io/) contiennent de nombreux exemples. 
Cela peut être également un bon moyen de comprendre les possibilités de Jekyll.

### Hébergement sur GitHub Pages
Jekyll est particulièrement bien intégré avec [GitHub Pages](https://pages.github.com/). C'est un service qui permet d'exposer 
des sites web publics en utilisant simplement un repository GitHub comme hébergement.
Précisons que le créateur de Jekyll est Tom Preston-Werner, co-fondateur de GitHub ! Ceci explique peut-être cela... 

En tout cas, en déposant simplement les fichiers *source* de Jekyll dans un repository GitHub, le site sera généré par Jekyll et exposé sur Internet. 
Par défaut, le nom de domaine sera *username.github.io* où *username* est le nom d'utilisateur GitHub. 

**Il est également possible d'utiliser un nom de domaine personnalisé.** 
Pour cela il faut ajouter un fichier **CNAME** qui contient l'URL du domaine personnalisé à la racine du repository. 
Selon le fournisseur choisi, il faudra également configurer les DNS entre GitHub et le fournisseur. Par exemple avec OVH, 
il faut créer une zone DNS de type A afin de faire pointer le domaine vers une des IP de GitHub. Voir la page [d'aide](https://help.github.com/en/articles/troubleshooting-custom-domains) sur le sujet.

## En bref
J'aime beaucoup la facilité d'utilisation et la simplicité de Jekyll. C'est une très bonne solution pour celui qui souhaite faire un blog assez simplement, sans s'encombrer d'un outil lourd à installer et administrer. Cela permet de se passer de Medium et de ses contraintes.

