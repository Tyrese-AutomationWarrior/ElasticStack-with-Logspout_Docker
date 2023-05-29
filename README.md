# Elastic stack (ELK) with Logspout  on Docker
[![Elastic Stack version](https://img.shields.io/badge/Elastic%20Stack-8.7.1-00bfb3?style=flat&logo=elastic-stack)](https://www.elastic.co/blog/category/releases)

Elastic stack (ELK) on Docker with Logspout
Run the 8.7.1 version of the Elastic stack and Logspout(log router for Docker containers) with Docker and Docker Compose.

It gives you the ability to analyze any data set by using the searching/aggregation capabilities of Elasticsearch and the visualization power of Kibana.

Based on the official Docker images from Elastic and Logspout image of [Gliderlabs](https://github.com/gliderlabs):

- [Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
- [Logstash](https://github.com/elastic/logstash/tree/main/docker)
- [Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)
- [Logspout](https://github.com/gliderlabs/logspout)



> **Warning**  
> Due to several disabled security elements, this project is only for learning purposes and not for production.
> For production configurations, I recommend that users configure their host according to the Elasticsearch documentation: [Important System Configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html)



## Philosophy

This project was built for those who want to experiment with the ELK stack for docker engine monitoring. It is not intended for production use because **all security features have been disabled**.
It is a reliable and quick method for learning how the ELK stack works with the Docker engine.

## Requirements

### Host setup

* [Docker Engine][docker-install] version **23.0.1** or newer
* [Docker Compose][compose-install] version **2.16.0** or newer (including [Compose V2][compose-v2])
* 1.5 GB of RAM
* OS should be Linux's kernel based (Arch, Ubuntu, RedHat)
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
> Elasticsearch's [bootstrap checks][https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html] were purposely disabled to facilitate the setup of the Elastic
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
docker-compose up -p elk
```

> **Note**  
> You can also run all services in the background (detached mode) by appending the `-d` flag to the above command.

Give each service about a minute to initialize(depending on your system parameters), then access the Kibana web UI by opening <http://localhost:5601> in a web
browser

### Cleanup

Elasticsearch data is persisted inside a volume by default.

In order to entirely shutdown the stack and remove all persisted data, use the following Docker Compose command:

```sh
docker-compose -p elk down -v
```

### How to configure Elasticsearch

The Elasticsearch configuration is stored in [`elasticsearch/config/elasticsearch.yml`](https://github.com/iskandaryansergey/elk-docker-compose/blob/main/elasticsearch/config/elasticsearch.yml).

Please refer to the following documentation page for more details about how to configure Elasticsearch inside Docker
containers: [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
You can also specify the options you want to override by setting environment variables inside the Compose file:

```yml
elasticsearch:
  environment:
    discovery.type: ${discovery_type}
    ES_JAVA_OPTS: -Xms512m -Xmx512m
    ES_USE_IPV4: ${ES_USE_IPV4}
```

### How to configure Kibana

The Kibana default configuration is stored in [`kibana/config/kibana.yml`](https://github.com/iskandaryansergey/elk-docker-compose/blob/main/kibana/config/kibana.yml).

Please refer to the following documentation page for more details about how to configure Kibana inside Docker containers:[Install Kibana with Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)

### How to configure Logstash

The Logstash configuration is stored in [`logstash/config/logstash.yml`](https://github.com/iskandaryansergey/elk-docker-compose/blob/main/logstash/config/logstash.yml).
The Logstash pipeline file is stored in [`logstash/pipeline/logstash.conf`](https://github.com/iskandaryansergey/elk-docker-compose/blob/main/logstash/pipeline/logstash.conf)
Please refer to the following documentation page for more details about how to configure Logstash inside Docker
containers: [Configuring Logstash for Docker](https://www.elastic.co/guide/en/logstash/current/docker-config.html)
You can also specify the options you want to override by setting environment variables inside the Compose file:

```yml
logstash:

  environment:
    LS_JAVA_OPTS: -Xms256m -Xmx256m
```

## JVM tuning

### How to specify the amount of memory used by a service

The startup scripts for Elasticsearch and Logstash can append extra JVM options from the value of an environment
variable, allowing the user to adjust the amount of memory that can be used by each component:

| Service       | Environment variable |
|---------------|----------------------|
| Elasticsearch | ES_JAVA_OPTS         |
| Logstash      | LS_JAVA_OPTS         |

To accomodate environments where memory is scarce (Docker Desktop for Mac has only 2 GB available by default), the Heap
Size allocation is capped by default in the `docker-compose.yml` file to 512 MB for Elasticsearch and 256 MB for
Logstash. If you want to override the default JVM configuration, edit the matching environment variable(s) in the
`docker-compose.yml` file.

For example, to increase the maximum JVM Heap Size for Logstash:

```yml
logstash:

  environment:
    LS_JAVA_OPTS: -Xms1g -Xmx1g
```

When these options are not set:

* Elasticsearch starts with a JVM Heap Size that is [determined automatically][es-heap].
* Logstash starts with a fixed JVM Heap Size of 1 GB.
