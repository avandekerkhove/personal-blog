---
layout: post
title:  "Pousser dans Google Container Registry depuis GitLab CI ?"
description: "Utiliser GitLab CI pour construire et pousser des images Docker dans Google Container Registry"
cover:  "/assets/gitlabciandgcr/cobra.jpg"
---

[GitLab CI](https://docs.gitlab.com/ee/ci/) est un super outil d'intégration continue. 
Malheureusement il est parfois moins bien intégré que ses concurrents vers des outils tiers. 
J'ai eu une problématique pour pousser mes images Docker depuis un *pipeline* GitLab CI vers [Google Container Registry](https://cloud.google.com/container-registry/) (le dépôt d'images Docker intégré à GCP - Google Cloud Platform).

Après pas mal d'essais, voici la solution la plus simple et la plus élégante à laquelle j'ai pu aboutir.

## Création du service account GCP

Afin de bien isoler les droits et permissions, nous allons créer un service account qui aura pour mission de déposer les images sur la container registry associée à notre projet GCP.
Je montre ici les opérations via l'interface web de GCP.
Voici les 2 éléments importants :
* Créer le service account __sans permission__ (nous y reviendrons ensuite)
* Créer une clé associée avec le bouton __Create key__ (voir ci-dessous). Choisir le format __JSON__ et télécharger le fichier correspondant.

![Création d'un service account](/assets/gitlabciandgcr/create-sa.png)

## Positionnement des permissions

Afin de stocker les images de la container registry, GCP utilise sa solution *Cloud Storage*. Un *bucket* (élément de base de Cloud Storage) est créé la première fois que l'on dépose une image dans la container registry.  
Le *bucket* qui est utilisé par la registry se nomme **eu.artifacts.[projet-GCP].appspot.com** ('*eu.*' étant le préfixe pour la zone Europe, à adapter le cas échéant).

Il faut donc permettre au service account d'écrire dans le bucket. Nous allons donc dans l'interface *Storage / Browser* sur GCP. Nous sélectionnons le bucket adéquat et éditons ses permissions : 

![Storage browser](/assets/gitlabciandgcr/storage-browser.png)

Il faut ajouter un membre au bucket. Nous pouvons ajouter le service account grâce à son mail (on retrouve ce mail facilement dans l'interface service accout au besoin). 
La subtilité est de lui donner le bon rôle. Pour une raison indéterminée les rôles de type "Storage" ne fonctionnent pas. Il faut sélectionner un rôle de type "Storage Legacy". 

Nous lui donnons donc le rôle **Storage Legacy Bucket Writer**.

![Storage browser](/assets/gitlabciandgcr/storage-browser-2.png)

La partie GCP est terminée, nous pouvons nous concentrer sur GitLab :)

## Paramétrage GitLab CI

### Création de la variable pour la clé du service account
 
Nous créons une variable de type *"File"* dans les paramètres CI/CD de GitLab CI. Stocker dans cette variable le contenu du fichier de la clé JSON associée au service account. Ceci permettra d'utiliser la clé dans notre pipeline.  
Appelons cette variable par exemple *GCP_SA_DEPLOYER*.

### Précisions complémentaires

__Méthode de login Docker__ : J'ai rencontré parfois des solutions non satisfaisantes qui alourdissent inutilement la configuration GitLab (installation du CLI google...). En fait l'instruction *docker login* peut utiliser une clé JSON pour se logger sur la registry voulue. Nous allons donc utiliser simplement l'instruction suivante :  

	docker login -u _json_key --password-stdin https://eu.gcr.io < $GCP_SA_DEPLOYER

__Utilisation du cache GitLab__ : nous allons utiliser la fonctionnalité de cache de GitLab CI afin d'optimiser la réutilisation des *layers* de Docker. 
Ceci permet d'optimiser le temps de construction de l'image. Cela se passe en récupérant d'abord l'image et en buildant avec l'instruction *--cache-from*.
Plus d'explications [ici](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#making-docker-in-docker-builds-faster-with-docker-layer-caching)
 
### Fichier .gitlab-ci.yml

Finalement, voici à quoi peut ressembler le fichier .gitlab-ci.yml pour déployer sur la Google Container Registry :

	image: docker:latest
	services:
	  - docker:dind
	
	stages:
	  - build-image
	
	build-image:
	  stage: build-image
	  before_script: 
	    - docker login -u _json_key --password-stdin $REGISTRY_HOST < $GCP_SA_DEPLOYER
	  script:
	    - docker pull $REGISTRY_IMAGE:latest || true
	    - docker build --cache-from $REGISTRY_IMAGE:latest --tag $REGISTRY_IMAGE:latest .
	    - docker push $REGISTRY_IMAGE:latest


Voilà, c'était pas si compliqué au final.. :)
