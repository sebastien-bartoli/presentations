# Google Maps : odysée au-delà de mon trajet de vacances

---

## C'est qui qui parle ?

Sébastien, 25 ans, développeur web

**Ma passion:** Le code

**Ma seconde passion:** Les cartes

Note:
Sébastien, je suis développeur web. Ma passion c'est le code. Ma seconde pasison, c'est les cartes. Du coup j'aime bien faire des deux. Je suis passé par des agences de comm, des agences web, puis maintenant je travaille pour les Scouts et Guides de France.

Depuis que je travaille dans le milieu du web, j'ai eu l'occasion de mettre en place quelques projets cartographiques dont j'aimerai vous partager l'experience.

---

## De quoi qu'on parle ?

Aller (un peu) plus loin que de la simple geolocalisation de point et des directions.

Et un peu de technique aussi, parce que c'est mon dada.

Note:
Google Maps, on connait tous pour aller chercher son trajet de vacances ou aller chez son copain. Boum, on sors son téléphone, on entre une addresse et hop, j'ai un itinéraire point a point. Facile, pratique.

Et pourtant, avec Google Maps (et ses copains OSM, CartoDB, Mapbox, etc.) on peut faire plein d'autres choses, pour tout un tas d'applications. 

Evidemment, tout ce que je vais montrer ici peut etre fait peu ou prou de la même manière avec les alternatives libres.

Et on parlera un peu technique aussi parce que j'aime bien ça.

---

### Avant de commencer

Note:
**Qui est dev ? Designer ? Carto ? Qui fait autre chose complètement ?**

===

## Mémoires de Civils

**Au début, il n'y avait que le Marker…**

Note:
=> [Memoires de Civils - le documentaire](http://memoiresdecivils.com/)
=> [Memoires de Civils - la carte](http://carte.memoiresdecivils.com/)

---

### C'est quoi ?

Un webdocumentaire sur l'occupation allemande de Paris du point de vue des civils.

- [Memoires de Civils - le documentaire](http://memoiresdecivils.com/)
- [Memoires de Civils - la carte](http://carte.memoiresdecivils.com/)

Note:
Un webdocumentaire sur l'occupation allemande de Paris du point de vue des civils, et une carte liée pour les elements de videos, les témoignages et les archives qui n'ont pas pu être intégrées au cut final. On veut pouvoir lier très précisement un lieu géographique avec un document. 

Puis, lorsqu'un musée a choisi d'exposer ce webdocumentaire dans sa collection, il a fallu créer une version entièrement hors-ligne.

---

### La technique

```javascript
marker = new google.maps.Marker({
        position: new google.maps.LatLng(medium.get('latitude'), medium.get('longitude')),
        icon: "/images/marker_" + (medium.get('type')) + ".png"
      });
```

Note: 
On connait tous ce bout de code, c'est peu ou prou celui d'exemple de Google.

---

Les markers c'est bien, les markers ca fait tout. Meme le café.

Mais les markers c'est lent.

**Avantage** : le fonctionnement basique de Google Maps ; complètement customisable

**Inconvenient** : Vite lourd ; Fausses solutions front pour contourner ca.

Note:
Les markers, ca deviens vite très lourd a gérer pour un ordinateur un peu faiblard ou un smartphone. Et ca oblige a utiliser des techniques détournées (telles que le clustering) pour essayer de controler au mieux les problèmes de performance.

---

Depuis refait avec un serveur de tiles OSM + leaflet pour la version hors-ligne.

===

## Carrefour

**…Et puis il y a eu Fusion Tables…**

Note: 
=> [Carrefour Map](https://www.facebook.com/carrefour/app/323982594356304/)
=> [Fusion Table](https://docs.google.com/spreadsheets/d/1NyOuCXRVUPpiMOWN_d9YrS-BrZsBt9e1-kiDr-z9Xb0/edit)

---

### C'est quoi ?

Carrefour voulait mettre sur sa page Facebook un moyen de voir et trouver le magasin le plus proche de chez soi. 

2000 magasins a maintenir => *super facile a mettre a jour*

- [Page Facebook de Carrefour France](https://www.facebook.com/carrefour/app/323982594356304/)
- [Export des magasins sur Google Sheets](https://docs.google.com/spreadsheets/d/1NyOuCXRVUPpiMOWN_d9YrS-BrZsBt9e1-kiDr-z9Xb0/edit)

---

### La technique

```javascript
function FusionTable(myquery){
	var FTQuery = null ;
	if( typeof(myquery) == 'undefined' ){
		FTQuery = {
			from: FTID,
			limit: 2000,
			select: StandardFTFields
		};
	} else {
		FTQuery = myquery ;
	}
	var FTStyles = [{ where: "store_type IN ('Hyper','Planet', 'Market', 'Drive')",
					  markerOptions: {
						iconName: (IconType == 'big')? "blu_circle" : "small_blue"
					  }},
					{ where: "store_type IN ('Montagne','Contact','City')",
					  markerOptions: {
						iconName: (IconType == 'big')? "grn_circle" : "measle_brown"
					  }},
					{ where: "store_type = 'Express'",
					  markerOptions: {
						iconName: (IconType == 'big')? "orange_circle" : "small_red"
					  }}];
	var FTOptions = {
		map: Map,
		heatmap: false,
		query: FTQuery,
		styles: FTStyles
	};
	if( typeof(myquery) == 'undefined' ){
		Layer = new google.maps.FusionTablesLayer(FTOptions);
		Layer.setMap(null);
	} else {
		Layer.setOptions(FTOptions);
		Layer.setMap(Map);
	}	
}

function CreateFTQuery(){
	if( $('#maps_store_type > div.active').length > 0 ){
		var sql = "" ;
	
		var typeresults = "";
		$('#maps_store_type > div.active').each(function(){
			typeresults += "'" + $(this).attr('data-type') + "',";
		});
		typeresults = typeresults.substring(0, typeresults.length - 1);
		sql = "store_type IN ("+ typeresults +")" ;

		var MyQuery = {
			from: FTID, 
			limit:2000, 
			select: StandardFTFields, 
			where: sql
		};
		FusionTable(MyQuery);
	} else {
		Layer.setMap(null);
	}
}
```

---

Solution hébergée sur Google Docs (bouh!). Mais, c'est super rapide. 

Le vrai début des layers chez Google.

**Avantage** : super performant ; on peut trier coté serveur. 

**Incovenient** : lourd a mettre en place ; peu de customisation.

Note:
Obligé d'avoir un code spécifique en plus pour convertir en format Google Docs, c'est relativement lourd.

===

## SGDF International 

**…Et enfin les layers, les vrais.**

Note:
=> [Carte internationale](https://www.sgdf.fr/vos-ressources/carte-internationale)

---

### C'est quoi ?

Les Scouts et Guides de France maintiennent depuis des années un gros fichier excel avec tout plein d'infos.

Comme ça parlait de l'international, on en a fait une carte.

- [Carte internationale](https://www.sgdf.fr/vos-ressources/carte-internationale)

Note:
Les Scouts et Guides de France (mon employeur donc), et specifiquement le service international, maintiens depuis plusieurs années un gros fichier excel avec tout un tas d'infos sur toutes les associations de scoutisme dans le monde, et tous les projets que l'on a pu faire avec eux, ainsi que tout un tas d'info que les jeunes qui partent a l'étranger vont avoir besoin pour préparer leur trajet.

Du coup, quoi de mieux qu'une carte pour montrer des données dont le principal facteur differenciant est le pays ? 

---

### La technique

```javascript
Data[MapType] = new google.maps.Data();
Data[MapType].loadGeoJson(jsonPath, {idPropertyName:geoJSONID});
Data[MapType].setStyle(dataStyle);
Data[MapType].setMap(Map);
```

---

2 layers, avec deux jeux de données complètement différents. 

Super simple a utiliser pour du basique.

**Avantage** : Très performant malgré le 1er chargement ; Simple ; Très customisable.

**Inconvenient** : Créer les GeoJSON, format un peu fluctuant et pas encore super démocratisé.

---

Du coup, on a mis du GeoJSON partout, pour aller plus vite ET pour simplifier les mises a jour : [Cartes des groupes](https://www.sgdf.fr/). 

Connecté a notre intranet, avec une mise a jour toutes les semaines et converti en GeoJSON.

===

## SGDF Bases

**Vers l'infini et au-delà !**

Note:
=> [Carte des bases](https://bases.sgdf.fr)

---

### C'est quoi ?

Une trentaine de lieux d'accueil.

Un vieux site, pas de recherche, des infos disparates. Il était temps pour une refonte.

Sauf que là, on va plus loin.

- [Carte des bases](https://bases.sgdf.fr)

Note:
On avait un site pour les differentes bases scoutes qui se faisait très vieillissant et qui avait une fonction de recherche pour ainsi dire inexistante. Très dur de savoir où aller et comment y aller. Et surtout, toutes les infos n'étaient pas toujours affichées de la même manière. 

Pour pouvoir afficher les infos, on a donné un excel a chacun (parce que c'est plus simple), et on est en train d'ajouter une interface d'admin pour permettre a chaque gérant de base de modifier rapidement et facilement ses données. 

Maintenant, c'est plus facile que jamais de savoir ce qu'il y a autour de la base en quelques clics.

---

### Techniquement

```javascript
DirectionsServices = new google.maps.DirectionsService();
Directions = new google.maps.DirectionsRenderer({
	suppressMarkers: true,
	InfoWindow: Infowindow,
	polylineOptions: {
		strokeColor: '#00a89d',
		strokeOpacity: .7,
		strokeWeight: 5
	}
});
Directions.setMap(Map);

MarkersListeners.push(google.maps.event.addListener(marker, 'click', function(){
	var GPSRequest = {
		origin: startPoint,
		destination: marker.getPosition(),
		travelMode: google.maps.TravelMode.DRIVING
	};
	DirectionsServices.route(GPSRequest, function(response, status){
		if (status == google.maps.DirectionsStatus.OK) {
			Directions.setMap(Map);
	      	Directions.setDirections(response);
	      	Infowindow.setContent(returnInfowindowContent(response.routes[0].legs[0], marker.getTitle()));
	      	var anchor = new google.maps.MVCObject();
			anchor.set('position', marker.getPosition());
			Infowindow.open(Map, anchor);
			google.maps.event.addListenerOnce(Infowindow, 'closeclick', function(){
				Directions.setMap(null);
			})
	    }
	});
}));
```
--- 

En appelant quelques APIs supplémentaires, on peut rapidement donner une nouvelle feature.

Note:
- GeoJSON encore, mais un seul cette fois-ci.
- Geocoding, Geometry et Directions
- Créer des points liés, avec le chemin, le temps estimé, augmenter les bounds ou pas en fonction de la distance ...

===

## Et vous ? 

Google Maps et les copains, c'est pas fait juste pour savoir où on est.

**Et vous, vous les utilisez pour quoi ?**
