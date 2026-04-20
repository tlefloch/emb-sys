# B. Résolution des dépendances

## Solution

A peine remis de vos émotions vous réfléchissez à une solution. Deux options s'offrent à vous :

1. Résoudre les problèmes de dépendances sur votre machine jusqu'à parvenir à faire fonctionner la simulation
2. Conteneuriser la simulation pour l'isoler de l'environnement de développement de la machine hôte

En résolvant les problèmes de dépendances localement, le risque est très élevé que le problème se reproduise en partageant votre travail avec quelqu'un d'autre.  
La solution est toute trouvée, il faut conteneuriser l'environnement de développement du seabot.

## Workflow

1. Création d'une image Docker via un Dockerfile
2. Build et run locaux pour vérifier le fonctionnement de la simulation
3. Livraison de l'application conteneurisée :
    - une image Docker poussée sur Docker Hub
    - une commande `docker run` pour lancer l'application



## Conteneurisation

Vous recevez quelques informations clés de la part de votre collègue:

- la simulation et le robot sont prévus pour fonctionner sous ROS2 Humble
- l'animation de visualisation de la simulation se fait dans une fenêtre matplotlib
- une fois le code source compilé, les noeuds ROS2 nécessairesà la simulation peuvent être lancés grâce au script `simulation_launch.py`

!!! Note "Problème n°1"
    Créez une nouvelle image Docker via un **Dockerfile** regroupant les dépendances de la simulation et permettant de la faire fonctionner.  
    Procédez comme suit :

    1. Partez d'une image de base minimale prévue pour des applications ROS2 Humble. Quelques recherches sont à faire sur DockerHub.
    2. Utilisez `docker build` pour créer votre image. Nommez la : **lab-seabot**
    3. Utilisez `docker run` pour lancer un conteneur en mode interactif que vous nommerez "seabot-simu" dans lequel vous pourrez tester la compilation et le lancement de la simulation.
    4. Vous rencontrerez des erreurs de compilation et d'exécution qui vous informeront sur les dépendances manquantes, rajoutez les dans le Dockerfile et recommencez à l'étape 2. Répétez jusqu'au fonctionnement de la simulation.

    La suite de cette page vous donne quelques infos bonnes à savoir.

    **Rendus attendus:**  
    Une fois ce premier problème résolu, c'est-à-dire jusqu'au fonctionnement de la simulation, vous rendrez :

    - le Dockerfile utilisé pour construire votre image
    - la commande `docker build` utilisé
    - la commande `docker run` permettant de build le code source

!!! Warning
    Il existe plusieurs images qui fournissent ROS2.  
    Attention à bien prendre la version humble, et une version minimale certes, mais qui contient les outils de compilation ROS2.

!!! Tip
    N'oubliez pas d'utiliser l'option `--rm` avec `docker run` pour éviter de conserver les conteneurs après exécution.

!!! Tip
    Dans Docker, il existe deux méthodes principales pour importer des données dans un conteneur :

    La première consiste à utiliser les commandes `ADD`/`COPY` dans les Dockerfiles. Celles-ci copient le contenu d'un fichier ou d'un dossier dans un répertoire spécifié du conteneur lors de sa création. Le principal avantage est la portabilité, car il n'est pas nécessaire de distribuer les fichiers sources de l'application ; ils sont tous inclus dans l'image Docker. Cependant, les modifications apportées aux fichiers de l'application ne sont pas facilement accessibles depuis la machine locale pour le développement.

    La seconde méthode utilise les volumes. Les volumes fonctionnent comme des liens symboliques entre un fichier ou un dossier local sur la machine hôte et un fichier ou un dossier à l'intérieur du conteneur. Le principal avantage de cette méthode est que toute modification apportée à ces fichiers est répercutée des deux côtés du volume. Cela la rend idéale pour les environnements de développement où le code source évolue rapidement.

    Source : [https://aschmelyun.com/blog/fixing-permissions-issues-with-docker-compose-and-php/](https://aschmelyun.com/blog/fixing-permissions-issues-with-docker-compose-and-php/)

    Nous choisirons la seconde méthode car nous aurons besoin par la suite de faire évoluer le code source.

!!! Tip
    Eigen est une bibliothèque dite "header-only".  
    Pour utiliser Eigen, il suffit de télécharger et d'extraire son code source (consultez le wiki pour les instructions de téléchargement). En réalité, les fichiers headers de Eigen sont les seuls nécessaires à la compilation des programmes. Ces fichiers headers sont identiques pour toutes les plateformes.

    Pour une utilisation avec cmake, il faut spécifier le chemin d'inclusion des headers dans le CMakeLists.txt

!!! Tip
    Vous aurez besoin de partager votre environnement d'affichage avec le conteneur, comme vous l'avez fait dans le **Lab 6**.  
    Pour les utilisateurs de Windows ou MacOS, vous devez installer un serveur X11 sur votre machine.


!!! Note "Problème n°2"

    Afin d'éviter de taper une série de commande dans le conteneur ou d'exécuter une commande à rallonge remplie de `&&`, il convient d'utiliser un script bash.  
    Dans le script `scripts/launch_simu.sh` vous écrivez les commandes à exécuter dans le conteneur pour lancer la simu. Ainsi il vous suffit d'exécuter ce script au démarrage du conteneur pour lancer la simulation.

    **Rendus attendus :**  

    - la commande `docker run` lancant directement la simulation dans le conteneur "seabot-simu"
    - le script `launch_simu.sh`