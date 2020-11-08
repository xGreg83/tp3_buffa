# Mini Projet - Application de recherche de restaurant

## Mise en place du projet
Via un terminal, placez vous dans le dossier serveur et exécutez les commandes suivantes :
```
npm install
```
```
node serverCrudWithMongo.js
```

Via un second terminal, placez vous à la racine du projet et exécutez les commandes suivantes :
```
npm install
```
```
npm run serve
```

Vous obtiendrez dans le terminal une adresse local de développement à utiliser via un navigateur pour voir le projet.

## Les requis
* NodeJS
* NPM
* MongoDB (utilisation de la base par défaut donnez dans le cours)

## Le projet
Il se présente sous la forme d'une application web. Les informations accessibles sont le nombre de restaurants et le nombre de page totale sur lesquels ils sont présentés.

On peut rechercher un restaurant en faisant défiler les pages, en augmentant le nombre de restaurant par page ou en le recherchant par nom dans la barre de recherche prévue à cet effet.

Pour chaque restaurant, on peux modifier son nom et le type de cuisine. On peut également le supprimer de manière définitive. On peut aussi obtenir plus d'informations sur un restaurant. Dans les détails d'un restaurant nous avons toujours son nom et le type de cuisine, ses différentes notations avec les dates corespondantes ainsi que son adresse complète et un affichage du restaurant sur une map dynamique.

Une notification s'affichera pour chaque restaurant afin de valider tout ajout, modification ou suppression. Pour chaque restaurant, l'obtention de plus d'informations affichera également une image alétoire différente à chacun.

L'ajout d'un restaurant nous permet de définir le nom et le type de cuisine. Si dans la barre de recherche, le restaurant recherché n'est pas trouvé, il nous sera proposé d'en ajouter un.

Tout les restaurants affichés sur la page de l'application sont également applicables à un tri sur leurs noms, cuisines et villes.
