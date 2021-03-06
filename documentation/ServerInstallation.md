Documentation - Mise en place du serveur SpawnKill
===============================================================

Prérequis
---------

- Un serveur web
- PHP (version minimum : 5.3.9)
- Un serveur MySQL (notamment pour le cache des données)
- Composer ([installation globale] recommandée)
- Node.js
- Git

Description
-----------

Le serveur de sockets de base est basé sur `Ratchet`.
Il permet de maintenir des liens entre des clients et des topics afin de les avertir quand les infos du topic sont mises à jour.
Le lien entre un client et un topic est fait, à la demande du client, par le message `startFollowingTopic`.
Les demandes de mises à jour sont effectuées à intervalle régulier par un "updater" client basé sur le module nodejs `websocket`. Ces demandes sont effectuées avec le message `updateTopicsAndPushInfos`. Seules les demandes de mises à jour envoyées depuis le serveur sont prises en compte.


Récupération du code
--------------------

Se placer à l'endroit souhaité et récupérer le dépôt de SpawnKill

```sh
git clone https://github.com/dorian-marchal/spawnkill
```

Le dépôt est cloné dans le répertoire `spawnkill`. J'appellerai ce répertoire, la "racine du dépôt".

Installation
------------

### Installation des dépendances PHP

Depuis la racine du dépôt :

```sh
cd server/socket
composer install
```

### Installation des dépendances Node

Depuis la racine du dépôt :

```sh
cd server/socket/bin
npm install
```


### Configuration du serveur

Ouvrir le fichier de configuration et ajuster les variables en fonction de votre configuration (depuis la racine du dépôt):

```sh
cp server/socket/src/SpawnKill/Config.default.php server/socket/src/SpawnKill/Config.php
nano server/socket/src/SpawnKill/Config.php
```

Faites de même avec le fichier de configuration pour le javascript

```sh
cp server/socket/bin/config.default.js server/socket/bin/config.js
nano server/socket/bin/config.js
```

Configurer ensuite la base de données (toujours depuis la racine du dépôt)

```sh
cp server/config.default.php server/config.php
nano server/config.php
```

Ce fichier permet aussi de configurer vos infos Github pour pouvoir proposer les mises à jour aux utilisateurs du script (voir la doc de Github pour obtenir le client_id et le client_secret)

Et lancer le script de création de la base de données (toujours depuis la racine du dépôt):

```sh
php5 server/create-database.php
```

### Lancer le serveur

Depuis la racine du dépôt

```sh
cd server/socket/bin
./start-server
```

Note : par défaut, le serveur se lance sur les ports 8080 et 8081, ceci peut être modifié dans le fichier de configuration.
`stdout` et `stderr` peuvent être redirigées vers un éventuel fichier de log de cette façon :

```sh
cd server/socket/bin
./start-server &> /var/log/spawnkill/server.log
```

Attention, si le shell est fermé, le serveur est coupé.
Pour éviter ça, il est possible d'utiliser screen pour détacher le processus du shell :

```sh
cd server/socket/bin
screen
./start-server &> /var/log/spawnkill/server.log
```

### Couper le serveur

Depuis la racine du dépôt

```sh
server/socket/bin/stop-server
```

Faire pointer le script vers le serveur
---------------------------------------

Afin que les utilisateurs de SpawnKill se connectent sur votre serveur, il faut modifier la configuration du script.

Dans le fichier `base.js`, modifiez les trois variables suivantes pour les faire pointer vers votre serveur :

```js
SERVER_URL: "http://serveur.spawnkill.fr/", // url `http` pointant vers le répertoire `/server` du dépôt spawnkill (avec un slash à la fin)
SOCKET_SERVER_URL: "ws://serveur.spawnkill.fr", // url `ws` pointant vers votre serveur
SOCKET_SERVER_PORT: 4243 //Port du serveur (correspond à `$SERVER_PORT` dans Config.php)
```

[installation globale]: https://getcomposer.org/doc/00-intro.md#globally