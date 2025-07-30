# HMP - Formation DevSecOps

## Prérequis
Avoir terminé le TP3.

## Jouer avec Juice Shop
Juice Shop est un terrain de jeu pour apprendre à exploiter et corriger les principales vulnérabilités. Cela peut être chronophage, mais cela permet une meilleure maîtrise des concepts de façon ludique. La documentation de l'OWASP est plutôt claire et saura vous guider dans la découverte des différentes vulnérabilités.

[Explication des défis](https://pwning.owasp-juice.shop/companion-guide/latest/part1/challenges.html)

Dans le tableau des scores, vous trouverez des objectifs étiquetés "Tutorial", mieux guidés pour commencer.

## Analyse de l'image Docker
Dans Docker Desktop, depuis la page des images (ou des conteneurs), dans la colonne Actions, cliquez sur les trois points, puis sur "View packages and CVEs" et lancez l'analyse. On retrouve un certain nombre de CVE (60). La fenêtre de gauche montre les couches de l'image Docker. On voit que toutes les CVE proviennent de la même couche.

Même si le code de l'application est volontairement vulnérable pour des démonstrations, l'image est construite en suivant des bonnes pratiques, dont le multi-staging.

Ouvrez le Dockerfile dans l'IDE. On y voit deux parties :

1. `FROM node:22` + instructions
2. `FROM gcr.io/distroless/nodejs22-debian12` + instructions

La première partie est utilisée pour l'installation à partir de l'image `node:22`. La deuxième partie est basée sur une image distroless, donc de petite taille avec une surface d'attaque réduite. Le dossier `/juice-shop` est copié dans la première image, puis dans la deuxième une fois l'installation effectuée.

Pour voir la différence, remplacez `FROM gcr.io/distroless/nodejs22-debian12` par `FROM node:22`. Dans le fichier `dockerhub.yml`, à la dernière ligne, remplacez le tag pour différencier les deux images. Poussez dans Git et, une fois l'image disponible dans Docker Hub, effectuez un pull de la nouvelle image et lancez l'analyse dans Docker Desktop. On voit de nombreuses vulnérabilités dans l'image de base.

## Bonnes et Mauvaise pratiques
1. *Moindre privilége*: 
Dans une image distoless on va retrouver l'utilisateur 0 (root) et l'utilisateur 65532 (non-root).
Dans la premiére partie on nomme l'utilisateur non-root propriétaire ('chown 65532') du dossier logs.
   On nomme ensuite le groupe root comme group propriétaire ('chgrp 0') de différents dossiers mais on limite leurs droits à ceux de l'utilisateur propiétaire ('chmod g=u').
Dans la deuxiéme partie on nomme l'utilisateur non-root et l'utilisateur root propriétaires ('chown=65532:0') du dossier juice-shop et on fait tourner le conteneur en tant qu'utilisatuer non root ('USER 65532')

2. *Utilisaton des labels*: L’utilisation de labels pour documenter l’image est une bonne pratique pour fournir des métadonnées utiles sur l’image, comme le mainteneur, la version, la licence, etc. Par exemple en cas de vulnérabilité détéctée sur le containeur on peut retrouver le mainteneur et mettre en place un plan de remédiation.

3. *Utilisation du tag latest*: Aucun tag n'est spécifié pour l'image distoless dons le latest est utilisé. C'est une bonne pratique de sécurité car l'image les plus récente est génralement la plus sécurisé car les pach de sécurité plus récent ont été appliqués. Cependant ce la nuit à la repoductibilité. Pour les étape de qualification et de production il est recommandé d'utiliser un tag. En cas d'incident le conteneur peut donc être relancé à l'identique. 

