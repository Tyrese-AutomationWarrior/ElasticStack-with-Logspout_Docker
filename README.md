# Elastic stack (ELK) with Logspout  on Docker
[![Elastic Stack version](https://img.shields.io/badge/Elastic%20Stack-8.7.1-00bfb3?style=flat&logo=elastic-stack)](https://www.elastic.co/blog/category/releases)

Elastic stack (ELK) on Docker  with Logspout
Run the 8.7.1 version of the Elastic stack and Logspout(log router for Docker containers that runs inside Docker) with Docker and Docker Compose.

It gives you the ability to analyze any data set by using the searching/aggregation capabilities of Elasticsearch and the visualization power of Kibana.

Based on the official Docker images from Elastic:

- [Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
- [Logstash](https://github.com/elastic/logstash/tree/main/docker)
- [Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)
- [Logspout](https://github.com/gliderlabs/logspout)



> **Warning**  
> This project is only for learning porpose not for production, due to many disabled security aspects.
> For production setups, I recommend users to set up their host according to the
> instructions from the Elasticsearch documentation: [Important System Configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html)



## Philosophy

This project was created for thoes who want to try ELK stack for the docker engine monitoring, it is not for production  due to all **security aspects was disabled**.
It is a robust and fast metod to get into and understand how ELK stack works with docker engine.

## Requirements

### Host setup

* [Docker Engine][docker-install] version **18.06.0** or newer
* [Docker Compose][compose-install] version **1.28.0** or newer (including [Compose V2][compose-v2])
* 1.5 GB of RAM
* OS should be Linux (Arch, Ubuntu, RedHat)
* Ensure that your user is able to work with docker daemon and interact with docker sock

> **Note**  
> Especially on Linux, make sure your user has the [required permissions][linux-postinstall] to interact with the Docker
> daemon. 

By default, the stack exposes the following ports:

* 5000: Logstash UDP input
* 9600: Logstash monitoring API
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana

> **Warning**  
> Elasticsearch's [bootstrap checks][bootstrap-checks] were purposely disabled to facilitate the setup of the Elastic
> stack in development environments. For production setups, I recommend users to set up their host according to the
> instructions from the Elasticsearch documentation: [Important System Configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html)

## Usage

### Bringing up the stack

Clone this repository onto the Docker host that will run the stack with the command below:

```sh
git clone https://github.com/iskandaryansergey/elk-docker-compose.git
```

Start stack components:

```sh
docker-compose up
```

> **Note**  
> You can also run all services in the background (detached mode) by appending the `-d` flag to the above command.

Give Kibana about a minute to initialize, then access the Kibana web UI by opening <http://localhost:5601> in a web
browser

### Cleanup

Elasticsearch data is persisted inside a volume by default.

In order to entirely shutdown the stack and remove all persisted data, use the following Docker Compose command:

```sh
docker-compose down -v
```

### How to configure Elasticsearch

The Elasticsearch configuration is stored in [`elasticsearch/config/elasticsearch.yml`][config-es].

Please refer to the following documentation page for more details about how to configure Elasticsearch inside Docker
containers: [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)

### How to configure Kibana

The Kibana default configuration is stored in [`kibana/config/kibana.yml`][config-kbn].

Please refer to the following documentation page for more details about how to configure Kibana inside Docker containers:[Install Kibana with Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)

### How to configure Logstash

The Logstash configuration is stored in [`logstash/config/logstash.yml`][config-ls].

Please refer to the following documentation page for more details about how to configure Logstash inside Docker
containers: [Configuring Logstash for Docker](https://www.elastic.co/guide/en/logstash/current/docker-config.html)
