# D. Cross-compilation

L'objectif final reste de faire fonctionner nos alforithmes sur le robot réel.  
Il faut donc compiler le code source en ciblant l'architecture du robot, puis téléverser les binaires compilés sur le robot.

!!! Note "Problème n°7"

    Cross-compilez le code du seabot pour une architecture `arm64` à la manière dont ça a été fait dans le lab 5.

    Nous ne voulons pas obtenir les binaires compilés pour arm64 au même endroit que nous obtenons ceux de l'architecture hôte.  
    Faites en sorte que le code source soit compilé dans le dossier `arm64`.

    Utilisez un script bash `build_arm64` pour exécuter la série de commande dans le conteneur.

    **Rendus attendus :**

    - la commande `docker build` de l'image multiplatforme
    - la commande `docker run` de la cross compilation
    - le script bash `build_arm64` exécuté dans le container