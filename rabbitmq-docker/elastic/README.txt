Refer to: https://www.elastic.co/guide/en/kibana/current/docker.html
Github: https://github.com/elastic/dockerfiles/tree/8.5/kibana
Published Docker images and tags:  www.docker.elastic.co

--------------------------------------------------------------------------------
Installl Kibana with Docker
--------------------------------------------------------------------------------

1. Start an Elasticsearch container for development or testing:
docker network create elastic
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.5.3
docker run --name es-node01 --net elastic -p 9200:9200 -p 9300:9300 -t docker.elastic.co/elasticsearch/elasticsearch:8.5.3


* Result:
✅ Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.

-️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
  FZb-mZavRh+aY2RWweu0

-️  HTTP CA certificate SHA-256 fingerprint:
  a80039bb79e3f4f0e2b4d08b3ba50bd9602a43b7d583e7bb8a26d353a83c5041

-️  Configure Kibana to use this cluster:
• Run Kibana and click the configuration link in the terminal when Kibana starts.
• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjUuMyIsImFkciI6WyIxOTIuMTY4LjE3Ni4yOjkyMDAiXSwiZmdyIjoiYTgwMDM5YmI3OWUzZjRmMGUyYjRkMDhiM2JhNTBiZDk2MDJhNDNiN2Q1ODNlN2JiOGEyNmQzNTNhODNjNTA0MSIsImtleSI6Ik94elVGSVVCRmdtSXlIdU5qc3RMOkI0cWRYV3RlUWNTd0JqU0RaQktxVWcifQ==

-️ Configure other nodes to join this cluster:
• Copy the following enrollment token and start new Elasticsearch nodes with `bin/elasticsearch --enrollment-token <token>` (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjUuMyIsImFkciI6WyIxOTIuMTY4LjE3Ni4yOjkyMDAiXSwiZmdyIjoiYTgwMDM5YmI3OWUzZjRmMGUyYjRkMDhiM2JhNTBiZDk2MDJhNDNiN2Q1ODNlN2JiOGEyNmQzNTNhODNjNTA0MSIsImtleSI6Ik9oelVGSVVCRmdtSXlIdU5qc3RJOncxYS01bWY0U3hPTzFuQVowQklYVEEifQ==

  If you're running in Docker, copy the enrollment token and run:
  `docker run -e "ENROLLMENT_TOKEN=<token>" docker.elastic.co/elasticsearch/elasticsearch:8.5.3`

2. In a new terminal session, start Kibana and connect it to your Elasticsearch container:
docker pull docker.elastic.co/kibana/kibana:8.5.3
docker run --name kib-01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.5.3

When you start Kibana, a unique link is output to your terminal.

3. To access Kibana, click the generated link in your terminal.

In your browser, paste the enrollment token that you copied when starting Elasticsearch and click the button to connect your Kibana instance with Elasticsearch.
Log in to Kibana as the elastic user with the password that was generated when you started Elasticsearch.

--------------------------------------------------------------------------------
Generate password and enrollment token
--------------------------------------------------------------------------------
1. To reset the password for the elastic user:

docker exec -it es-node01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

2. To generate an enrollment token:

docker exec -it es-node01 /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

--------------------------------------------------------------------------------
Remove docker container
--------------------------------------------------------------------------------
docker network rm elastic
docker rm es-node01
docker rm kib-01

--------------------------------------------------------------------------------
Configure Kibana on Docker
--------------------------------------------------------------------------------
The setting of kibana.yml can be found here: https://www.elastic.co/guide/en/kibana/8.5/settings.html

There are also other ways to configure Kibana:

1. Bind-mouted configuration

version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:8.5.3
    volumes:
      - ./kibana.yml:/usr/share/kibana/config/kibana.yml

2. Persist the Kibana keystore

docker run -it --rm -v full_path_to/config:/usr/share/kibana/config -v full_path_to/data:/usr/share/kibana/data docker.elastic.co/kibana/kibana:8.5.3 bin/kibana-keystore create

docker run -it --rm -v full_path_to/config:/usr/share/kibana/config -v full_path_to/data:/usr/share/kibana/data docker.elastic.co/kibana/kibana:8.5.3 bin/kibana-keystore add test_keystore_setting

3. Configure Kibana via env variables:

SERVER_NAME = server.name

SERVER_BASEPATH = server.basePath

ELASTICSEARCH_HOSTS = elasticsearch.hosts

version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:8.5.3
    environment:
      SERVER_NAME: kibana.example.org
      ELASTICSEARCH_HOSTS: '["http://es01:9200","http://es02:9200","http://es03:9200"]'

4. Docker defaultsedit
The following settings have different default values when using the Docker images:

server.host = "0.0.0.0"

server.shutdownTimeout = "5s"

elasticsearch.hosts = http://elasticsearch:9200

Monitoring.ui.container.elasticsearch.enabled = true
