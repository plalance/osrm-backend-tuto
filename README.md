# Projet
Étapes pour lancer un OSRM back sous docker avec des éléments cartographiques chargés.

# Pré-requis :
- Télécharger les données cartographiques voulues (http://download.geofabrik.de)
- Avoir docker installé sur le poste.
- Placer les données dans un répertoire qui sera partagé dans le doker plus tard.

Pour l'exemple, les données de franche-compté (http://download.geofabrik.de/europe/france/franche-comte.html)
sont prises et placées dans le répertoire C:\temp\osrm\data.

Pour l'exemple, le répertoire hôte choisi est : /c/temp/osrm/data

## Étape 1 : Extraire les données cartographiques.
    docker run -ti -v /c/temp/osrm/data:/data osrm/osrm-backend osrm-extract -p /opt/car.lua /data/franche-comte-latest.osm.pbf

## Étape 2 : partitionner puis préparer les données extraites (commandes docker à lancer).
    docker run -ti -v /c/temp/osrm/data:/data osrm/osrm-backend osrm-partition /data/franche-comte-latest.osrm
    docker run -ti -v /c/temp/osrm/data:/data osrm/osrm-backend osrm-customize /data/franche-comte-latest.osrm

## Étape 3 : Lancer le serveur OSRM backend de routing.

    docker run --name osrm-back -ti -p 5000:5000 -d -v /c/temp/osrm/data:/data osrm/osrm-backend osrm-routed --algorithm mld /data/franche-comte-latest.osrm

    docker run --name osrm-back -ti -p 5000:5000 -d -v /c/temp/osrm/data:/data osrm/osrm-backend osrm-routed --algorithm mld /data/rhone-alpes-latest.osrm

## Étape 4 : Tester un initénaire avec au moins 2 steps.

Lieu 1 :
47.655677,6.849583

Lieu 2 :
47.476554,6.840107

http://127.0.0.1:5000/route/v1/driving/6.849583,47.655677;6.840107,47.476554?steps=true

http://127.0.0.1:5000/route/v1/driving/47.655677,6.849583;47.476554,6.840107?steps=true


## Étape optionnelle : lancer un frontend pour tester

    docker run --name osrm-front -p 9966:9966 -d osrm/osrm-frontend

Tester :
http://127.0.0.1:9966

Tester :
Plusieurs points :
http://127.0.0.1:5000/trip/v1/driving/6.119771,45.457973;6.074465,45.393063;5.991355,45.372189;6.023098,45.432449;6.021273,45.431934;6.063541,45.502048;6.155733,45.56929

