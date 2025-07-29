# HMP - Formation DevSecOps

## Pré-requis :
Voir TP3

## Jouer avec Juice Shop
Juice Shop est un terrain de jeux pour apprendre à exploiter et corriger les principales vulnérabilité. Ca peut ête chronophage mais ça permet une meilleur maitrise des conceptes de façon ludique. 
La documentation de l'OWASP est plutôt claire et sera vous guider dans la découverte des différentes vulnérabilités.

[Explication des challenges](https://pwning.owasp-juice.shop/companion-guide/latest/part1/challenges.html)

Dans le leaderboard vous trouverez des objectifs tagués "Tutorial" mieux guidés pour commencer.

## Analyse de l'image Docker
Dans docker desktop, depuis la page images (ou containers) dans la colonne Actions cliquez sur les trois points> "View packages and CVEs" et lancez l'analyse. On retrouve un certain nombre de CVE(60) 
La fenêtre de gauche montre les couches de l'image docker. On voit que toutes les CVEs ont issues de la même couche. 
Même si le code de l'application est volontairement vulnérable pour faire des démonstration, l'image est construite en suivant des bonnes pratiques dont le multi staging. 

Ouvrez le DockerFile dans l'IDE. 
On y voir deux parties: 
> "FROM node:22" + instructions
> 
> "FROM gcr.io/distroless/nodejs22-debian12" + instructions
  
La premiére partie est utilisée pour l'installation à partir de l'image node:22
La deuxiéme partie est basée sur une image distoless donc de petite tailles avec moins de surface d'attaque. Le dossier /juice-shop est copier dans la premiére image puis dans la deuxiéme une fois l'installation effectué.

Pour voir la différence remplace FROM gcr.io/distroless/nodejs22-debian12 par FROM node:22. Dans le dockerhub.yml à la derniére ligne remplace le tag pour différencer les deux images. Pousse dans git et une fois l'image disponible dans docker hub, pull la nouvelle image et lance l'analyse dans docker desktop. On voit de nombreuses vulnérabilités dans l'image de base. 


