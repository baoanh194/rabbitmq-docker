Refer to: https://www.elastic.co/guide/en/kibana/current/docker.html
Github: https://github.com/elastic/dockerfiles/tree/8.5/kibana
Published Docker images and tags:  www.docker.elastic.co


1. Start an Elasticsearch container for development or testing:
docker network create elastic
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.5.3
docker run --name es-node01 --net elastic -p 9200:9200 -p 9300:9300 -t docker.elastic.co/elasticsearch/elasticsearch:8.5.3
