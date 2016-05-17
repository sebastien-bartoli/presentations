# Google Maps : odysée au dela de mon trajet de vacances.

## Intro 

### Le qui

Sébastien, je suis développeur web. Ma passion c'est le code. Ma seconde pasison, c'est les cartes. Du coup j'aime bien faire des deux. Je suis passé par des agences de comm, des agences web, puis maintenant je travaille pour les Scouts et Guides de France.

Depuis que je travaille dans le milieu du web, j'ai eu l'occasion de mettre en place quelques projets cartographiques dont j'aimerai vous partager l'experience.

### Le quoi

Google Maps, on connait tous pour aller chercher son trajet de vacances ou aller chez son copain. Boum, on sors son téléphone, on entre une addresse et hop, j'ai un itinéraire point a point. Facile, pratique.

Et pourtant, avec Google Maps (et ses copains OSM, CartoDB, Mapbox, etc.) on peut faire plein d'autres choses, pour tout un tas d'applications. 

Evidemment, tout ce que je vais montrer ici peut etre fait peu ou prou de la même manière avec les alternatives libres.

Et on parlera un peu technique aussi parce que j'aime bien ça.

### La salle

**Qui est dev ? Designer ? Carto ? Qui fait autre chose complètement ?**



## Mémoires de Civils - Au début, il n'y avait que le Marker

=> [Memoires de Civils - le documentaire](http://memoiresdecivils.com/)
=> [Memoires de Civils - la carte](http://carte.memoiresdecivils.com/)

### C'est quoi ?

Un webdocumentaire sur l'occupation allemande de Paris du point de vue des civils, et une carte liée pour les elements de videos, les témoignages et les archives qui n'ont pas pu être intégrées au cut final. On veut pouvoir lier très précisement un lieu géographique avec un document. 

Puis, lorsqu'un musée a choisi d'exposer ce webdocumentaire dans sa collection, il a fallu créer une version entièrement hors-ligne.

### La technique

- Les markers c'est bien, les markers ca fait tout. Meme le café.
- Mais les markers c'est lent
- Depuis refait avec un serveur de tiles OSM + leaflet pour la version hors-ligne.

**Avantage** : le fonctionnement basique de Google Maps, complètement customisable

**Inconvenient** : ca deviens vite très lourd a gérer pour un ordinateur un peu faiblard ou un smartphone. Et ca oblige a utiliser des techniques détournées (telles que le clustering) pour essayer de controler au mieux les problèmes de performance.


## Carrefour - Et puis il y a eu Fusion Tables

=> [Carrefour Map](https://www.facebook.com/carrefour/app/323982594356304/)
=> [Fusion Table](https://docs.google.com/spreadsheets/d/1NyOuCXRVUPpiMOWN_d9YrS-BrZsBt9e1-kiDr-z9Xb0/edit)

### C'est quoi ?

Carrefour voulais mettre sur sa page Facebook un moyen de voir et trouver le magasin le plus proche de chez soi. Avec presque 2000 magasins a maintenir, il fallait que ce soit très simple a mettre a jour.

### La technique

- En 2012, encore en beta
- Afficher 2000 points en un eclair avec les layers
- Mais possibilités de customisations un peu légères & mise en place un peu lourde (a l'epoque de la Beta, maintenant ca a changé)

**Avantage** : c'est super performant. On affiche 2000 points en moins de temps qu'il ne faut pour créer 3 markers standards. On peut trier. 

**Incovenient** : lourd a mettre en place, peu de customisation.


## SGDF International - Et enfin les layers, les vrais

=> [Carte internationale](https://www.sgdf.fr/vos-ressources/carte-internationale)

### C'est quoi ?

Les Scouts et Guides de France (mon employeur donc), et specifiquement le service international, maintiens depuis plusieurs années un gros fichier excel avec tout un tas d'infos sur toutes les associations de scoutisme dans le monde, et tous les projets que l'on a pu faire avec eux, ainsi que tout un tas d'info que les jeunes qui partent a l'étranger vont avoir besoin pour préparer leur trajet.

Du coup, quoi de mieux qu'une carte pour montrer des données dont le principal facteur differenciant est le pays ? 

### La technique

- 2 layers GeoJSON, fusionnés programmatiquement
- Un pour les coordonnées geographiques des pays
- Un pour les données scoutes, classées par pays
- Un fichier excel de base (maintenu par le service depuis longtemps), converti en GeoJSON

**Avantage** : c'est tres performant. Un peu moins que FusionTables (puisque les données sont chez nous et pas chez Google cette fois-ci). Plein de customisation possible. Facile a charger.

**Inconvenient** : Créer les GeoJSON, format un peu fluctuant et pas encore super démocratisé.


Du coup, on a mis du GeoJSON partout, pour aller plus vite ET pour simplifier les mises a jour : [Cartes des groupes](https://www.sgdf.fr/). Connecté a l'intranet, avec une mise a jour toutes les semaines et converti en GeoJSON.

## SGDF Bases - Et avec quelques APIs toutes betes, on peut aller un peu plus loin

=> [Carte des bases](https://bases.sgdf.fr)

### C'est quoi ?

On avait un site pour les differentes bases scoutes qui se faisait très vieillissant et qui avait une fonction de recherche pour ainsi dire inexistante. Très dur de savoir où aller et comment y aller. Et surtout, toutes les infos n'étaient pas toujours affichées de la même manière. 

Pour pouvoir afficher les infos, on a donné un excel a chacun (parce que c'est plus simple), et on est en train d'ajouter une interface d'admin pour permettre a chaque gérant de base de modifier rapidement et facilement ses données. 

Maintenant, c'est plus facile que jamais de savoir ce qu'il y a autour de la base en quelques clics.

### Techniquement

- GeoJSON encore, mais un seul cette fois-ci.
- Geocoding, Geometry et Directions
- Créer des points liés, avec le chemin, le temps estimé, augmenter les bounds ou pas en fonction de la distance ...

## Conclusion

Google Maps et les copains, c'est pas fait juste pour savoir où on est, et comment allez chez bidule. 