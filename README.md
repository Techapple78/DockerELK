# Elastic Stack (ELK) sur Docker

Lance un stack "Elastic Stack" [Elastic stack](https://www.elastic.co/elk-stack) avec Docker and Docker Compose.



## Contenu

Basé sur les dépots officiel Docker, Elastic, Ansible:

* [elasticsearch](https://github.com/elastic/elasticsearch-docker)
* [logstash](https://github.com/elastic/logstash-docker)
* [kibana](https://github.com/elastic/kibana-docker)
* [ansible](https://github.com/ansible/ansible)

Autres outils utilisés :
* [gosu](https://github.com/tianon/gosu)
* [dumb-init](https://github.com/Yelp/dumb-init) Superviseur de process et initialisateur de system pour Docker
* [tini](https://github.com/krallin/tini) Protection de la création de process Zombie


## Pré-requis

### Configuration

1. Installer [Docker](https://www.docker.com/community-edition#/download) version **1.10.0+**

2. Installer [Docker Compose](https://docs.docker.com/compose/install/) version **1.6.0+**

Installation de docker et docker-compose sur deb/ubuntu
```console
sudo apt-get remove docker docker-engine docker.io -y
sudo apt install docker.io -y
sudo apt install docker-compose -y
sudo systemctl start docker
sudo systemctl enable docker
```
3. Cloner ce Repository

## Utilisation

### Deployer le stack
**NOTE**: Avant de pouvoir utiliser le stack, Docker-compose va devoir builder les images.
Pour construire les images a partir du github

```console
$cd /tmp
$git clone https://github.com/Techapple78/DockerELK.git
$cd DockerELK
$docker-compose build
```
Pour commencer a utiliser le stack `docker-compose`:

```console
$ docker-compose up
```

Vous pouvez aussi lancer tous les services en arrière plan (detached mode) en ajoutant le paramètre `-d` de la commande ci-dessus.

Pour appeler Kibana après son inialisation, accéder à [http://localhost:5601](http://localhost:5601) à partir d'un navigateur.

Par défaut, le stack écoute les ports suivants:
* 5044: Logstash TCP input.
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana

Pour accéder au Bash d'un container, exemple pour Elactic par nom d'image :
```console
$docker run -it dockerelk_
```
ou par id généré après lancement :
```console
$docker container ls
//retrouver le ID de l'image dockerelk_elastic
$docker run -it ID
```


Générer des logs :
```console
$ nc localhost 5044 < /path/to/logfile.log
```

### Configuration initiale de Kibana par Ansible

 Création d'un index pattern par défaut dans Kibana par Ansible
 ```yml
- name: Initialisation d'un index pattern par défaut
  uri:
    url: http://localhost:5601/api/saved_objects/index-pattern
    method: POST
    body: "{'attributes':{'title':'logstash-*','timeFieldName':''@timestamp'}}'"
    body_format: json
    headers:
      Content-Type: "application/json"
      kbn-version: 6.3.0
```
### Configuration initiale de Kibana par CURL (bash)
```console
$ curl -XPOST -D- 'http://localhost:5601/api/saved_objects/index-pattern' \
    -H 'Content-Type: application/json' \
    -H 'kbn-version: 6.3.0' \
    -d '{"attributes":{"title":"logstash-*","timeFieldName":"@timestamp"}}'
```



elasticsearch.yml for configuring Elasticsearch
jvm.options for configuring Elasticsearch JVM settings
log4j2.properties for configuring Elasticsearch logging

Path settings
Cluster name
Node name
Network host
Discovery settings
Heap size
Heap dump path
GC logging
