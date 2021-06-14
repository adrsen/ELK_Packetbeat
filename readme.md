# Project : Manipulating ELK Stack

Projet de l'UE INF726 - sécurité des SI du Master Big Data de Telecom paris 2021 

## Description

L'objectif du projet est de construire la stack ELK et la manipuler :
* Construction de l'environnement
* Réaliser une collecte de données (beats/packetbeat)
* Traiter un fichier pcap, source https://www.netresec.com/?page=pcapfiles
* Réaliser une indexation des données (packetbeat+elasticsearch)
* Construire un dashboard et présenter les données (dashboard kibana)


## Architecture
L'ensemble de l'architecture est encapsulé dans des conteneurs docker et orchestré par docker compose afin de simplifier le deploiement.
### Dependencies

* Docker Engine (version 19.03.8)
* Docker Compose (version 1.25.0)
* 4 GB de RAM

### Stack simple

![Pipeline][pipeline-simple]

### Install

Se rendre dans le dossier du repo et lancer la commande suivante afin d'installer la stack :
```
 $ docker-compose up
```

aprés téléchargement des images docker, de l'installation et du lancement des services l'inteface Kibana sera accessible sur le port 5601:
http://127.0.0.1:5601.

Pour arreter les services appuyez sur crlt + c ou bien:
```
$ docker-compose down
```

### ingestion des données

2 facons d'importer les données 
1.  les loader directement dans Kibana  
Pour cela il faudra au préalable convertir le fichier pcap en json à l'aide de wireshark
donc installer wireshark ourvir un terminal et taper la commande:
```
$ tshark -r nom_du_fichier.pcap -T ek > packets.json 
```
puis se rendre dans le module upload data de Kibana
![import-data][import-kibana]

2. Loader les données via packetbeat.  
Pour cela se connecter en terminal dans le conteneur packetbeat :

```
$ docker exec -it packetbeat /bin/bash 
```
et après s'être connecté au conteneur on peut executer ensuite la commande :


```
$  ./packetbeat -e -c packetbeat.yml -t -I data/nom_du_fichier.pcap -d "publish" -strict.perms=false 
```

### Index
on cree ensuite un index afin de pouvoir requeter les données dans Kibana

![index-create][index-kibana]


### Dasboard
Les données sont ensuite accessible dans Kibana il ne reste plus qu'à selectionner les champs à étudier et construire des visualisations
afin d'obtenir un dashboard comme le suivant :

![Dashboard][dash-V7]

### Stack complète
un dossier full stack comprend egalement l'implémentation de la complète stack ELK v5.1.1 + beats en suivant l'article du blog Elastic.co
[Full Stack ELK][fullstack-url]  
La stack est plus complete car il y a de préconfiguré le monitoring des flux reseaux locaux ainsi que le monitoring de services lancés comme une BDD mysql, un serveur nginx et un serveur apache. Les données remontées dans Elastic search via tout les services de beats : 
* Metricbeat
* Filebeat 
* Hearbeat 
* et enfin packetbeat pour le traitement des fichier de log  réseaux

### pipeline full Stack

![Pipeline][pipeline-full]

## Ressources

Inspiration, code snippets, etc.
* [Full Stack ELK - elastic blog][fullstack-url]
* [Analyzing Network Packets with Wireshark, Elasticsearch, and Kibana- elastic blog][blog-elastic]
* [Deploy ELK tutorial][github-YT]
* [Packetbeat quick start][packetbeat-doc]

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->

[fullstack-url]: https://www.elastic.co/fr/blog/a-full-stack-in-one-command
[pipeline-simple]: images/pipeline-simple.png 
[pipeline-full]: https://api.contentstack.io/v2/uploads/59a07b510a39c22f2827c579/download?uid=bltbd9848f3161729a2
[dash-V7]: images/dash-V7.png
[blog-elastic]:https://www.elastic.co/fr/blog/analyzing-network-packets-with-wireshark-elasticsearch-and-kibana
[github-YT]:https://github.com/justmeandopensource/elk
[packetbeat-doc]:https://www.elastic.co/guide/en/beats/packetbeat/current/packetbeat-installation-configuration.html
[import-kibana]: images/import.png
[index-kibana]:images/index.png
