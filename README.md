# Ultimate Docker Compose for (Kafka, Zookeeper, KSQL, Postgres, MongoDB, ArangoDB, Oracle, ClickHouse, PACT, Jaeger and more)

*Docker Compose Version 2.3* for all services that I was working with or configured while developing.

Just in case I need to *ready-to-run* them all so I can do it from one place as *Docker-Compose*.
Or as a builder with configured docker compose dependencies.

All services have specified dependencies and configured to use each other.

- [RDBMS:](#RDBMS)
    - [Postgres:](#Postgres)
        - [PG Admin 4](#Postgres-Admin)
    - [Oracle](#Oracle)
    - [Cockroach](#Cockroach)


- [Document Oriented](#Document-Oriented)
    - [MongoDB](#MongoDB)
    - [ArangoDB](#ArangoDB)
    - [ArangoDB Cluster](#ArangoDB-Cluster)


- [Key Value:](#Key-Value)
    - [Redis](#Redis)
        - [CLI](#Redis-CLI)
    - [Infinispan](#Infinispan)
    - [Hazelcast](#Hazelcast)


- [Column-oriented DBMS:](#Column-Oriented-DBMS)
    - [Cassandra](#Cassandra)
    - [ClickHouse:](#ClickHouse)
        - [CLI](#ClickHouse-CLI)
        - [JDBC-Bridge](#ClickHouse-JDBC-Bridge)
        - [Redash](#Redash)


- [Graph Databases:](#Graph-Databases)
    - [JanusGraph](#JanusGraph)
    - [ArangoDB](#ArangoDB-Graph)


- [Search Engines:](#Search-Engines)
    - [ElasticSearch](#ElasticSearch)
    - [ArangoDB](#ArangoDB-Search)


- [Kafka infrastructure:](#Kafka-infrastructure)
    - [Kafka](#Kafka)
    - [Zookeeper](#Zookeeper)
    - [Kafka Topics](#Kafka-Topics)
    - [Kafka REST](#Kafka-REST)
    - [Schema Registry](#Schema-Registry)
    - [KSQL:](#KSQL)
        - [Client](#KSQL-Client)
        - [Control Center](#KSQL-Control-Center)


- [Message Brokers:](#message-brokers)
    - [Kafka](#kafka)
    - [NATS](#nats)


- [Big Data:](#Big-Data)
    - [Apache Nifi](#Apache-Nifi)


- [Infrastructure:](#Infrastructure)
    - [Minio (S3)](#Minio)
    - [Jaeger](#Jaeger)
    - [PACT Broker](#PACT-Broker)
    - [SonarQube](#SonarQube)
    - [LiquiBase](#LiquiBase)
    - [Apache Atlas](#Apache-Atlas)
    - [GraphQL Apollo Federation](#GraphQL-Apollo-Federation)


## Databases

### RDBMS

RDBMS stands for Relational Database Management System. 
RDBMS is the basis for SQL, and for all modern database systems like Postgres, Oracle, MySQL and etc.

#### Postgres

For more info - [check here](https://hub.docker.com/_/postgres/).

```dockerfile
version: '2.3'
services:
  postgres:
    image: postgres
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '5432:5432'
    environment:
      POSTGRES_DB: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
```

##### Postgres Admin

For more info - [check here](https://hub.docker.com/r/dpage/pgadmin4).

Depends on [Postgres](#Postgres).

```dockerfile
version: '2.3'
services:
  pgadmin4:
    image: dpage/pgadmin4
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8010:80'
    depends_on:
      - postgres
    environment:
      PGADMIN_DEFAULT_EMAIL: bob@gmail.com
      PGADMIN_DEFAULT_PASSWORD: postgres
```

#### Oracle

For more info - [check here](https://hub.docker.com/_/oracle-database-enterprise-edition).

```dockerfile
version: '2.3'
services:
  oracle:
    image: store/oracle/database-enterprise:12.2.0.1
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '1521:1521'
      - '5500:5500'
```

#### Cockroach

For more info - [check here](https://hub.docker.com/r/cockroachdb/cockroach).

```dockerfile
version: '2.3'
services:
  cockroach:
    image: cockroachdb/cockroach
    restart: unless-stopped
#    logging:
#      driver: none
    command: start --insecure
    ports:
      - '26257:26257'
      - '10000:8080'
```

### Document Oriented

Document store databases store each record and its associated data within a single document. 
Each document contains semi-structured data that can be queried against using various query and analytics tools of the DBMS.

#### MongoDB

For more info - [check here](https://hub.docker.com/_/mongo).

```dockerfile
version: '2.3'
services:
  mongo:
    image: mongo
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '27017:27017'
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongo
      MONGO_INITDB_ROOT_PASSWORD: mongo
      MONGO_INITDB_DATABASE: public
```

#### ArangoDB

For more info - [check here](https://hub.docker.com/_/arangodb).

```dockerfile
version: '2.3'
services:
  arangodb:
    image: arangodb
    restart: unless-stopped
#    logging:
#      driver: none
    restart: unless-stopped
    ports:
      - '8529:8529'
    environment:
      ARANGO_NO_AUTH: 1
```

##### ArangoDB Cluster

For more info - [check here](https://www.arangodb.com/docs/stable/architecture-deployment-modes-cluster.html).

```dockerfile
version: '2.3'
services:
  arangodb-agent1:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication false
              --agency.my-address tcp://arangodb-agent1:8529
              --agency.endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --agency.activate true
              --agency.size 3
              --agency.supervision true
              --database.directory /var/lib/arangodb3/agency1'

  arangodb-agent2:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication false
              --agency.my-address tcp://arangodb-agent2:8529
              --agency.endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --agency.activate true
              --agency.size 3
              --agency.supervision true
              --database.directory /var/lib/arangodb3/agency2'
    depends_on:
      - arangodb-agent1

  arangodb-agent3:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication false
              --agency.my-address tcp://arangodb-agent3:8529
              --agency.endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --agency.activate true
              --agency.size 3
              --agency.supervision true
              --database.directory /var/lib/arangodb3/agency3'
    depends_on:
      - arangodb-agent1

  arangodb-coordinator1:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-coordinator1:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info coord1
              --cluster.my-role COORDINATOR
              --database.directory /var/lib/arangodb3/arangodb-coordinator1'
    ports:
      - '8529:8529'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3

  arangodb-coordinator2:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-coordinator2:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info coord2
              --cluster.my-role COORDINATOR
              --database.directory /var/lib/arangodb3/arangodb-coordinator2'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3

  arangodb-coordinator3:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-coordinator3:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info coord3
              --cluster.my-role COORDINATOR
              --database.directory /var/lib/arangodb3/arangodb-coordinator3'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3

  arangodb-db1:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-db1:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info arangodb-db1
              --cluster.my-role DBSERVER
              --database.directory /var/lib/arangodb3/arangodb-db1'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3

  arangodb-db2:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-db2:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info arangodb-db2
              --cluster.my-role DBSERVER
              --database.directory /var/lib/arangodb3/arangodb-db2'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3

  arangodb-db3:
    image: arangodb/arangodb:3.7.11
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ARANGO_NO_AUTH: 1
    command: 'arangod --server.authentication=false
              --cluster.my-address tcp://arangodb-db3:8529
              --cluster.agency-endpoint tcp://arangodb-agent1:8529
              --server.endpoint tcp://0.0.0.0:8529
              --cluster.my-local-info arangodb-db3
              --cluster.my-role DBSERVER
              --database.directory /var/lib/arangodb3/arangodb-db3'
    depends_on:
      - arangodb-agent1
      - arangodb-agent2
      - arangodb-agent3
```

### Key Value

The key-value part refers to the fact that the database stores data as a collection of key/value pairs. 
This is a simple method of storing data, and it is known to scale well.

#### Redis

For more info - [check here](https://hub.docker.com/_/redis).

```dockerfile
version: '2.3'
services:
  redis:
    image: redis
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '6379:6379'
    command: redis-server --requirepass test
```

##### Redis CLI

For more info - [check here](https://hub.docker.com/r/rediscommander/redis-commander).

Depends on [Redis](#Redis).

```dockerfile
version: '2.3'
services:
  redis-commander:
    image: rediscommander/redis-commander
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '6380:8081'
    environment:
      REDIS_HOSTS: local:redis:6379:0:test
```

#### Infinispan

For more info - [check here](https://hub.docker.com/r/infinispan/server).

```dockerfile
version: '2.3'
services:
  infinispan:
    image: infinispan/server
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '11222:11222'
    environment:
      USER: admin
      PASS: admin
```

#### Hazelcast

For more info - [check here](https://hub.docker.com/r/hazelcast/hazelcast).

```dockerfile
version: '2.3'
services:
  hazelcast:
    image: hazelcast/hazelcast
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '5701:5701'
    environment:
      JAVA_OPTS: -Dhazelcast.shutdownhook.policy=GRACEFUL
```

### Column Oriented DBMS

Columns store databases use a concept called a keyspace. A keyspace is kind of like a schema in the relational model. 
The keyspace contains all the column families (kind of like tables in the relational model), which contain rows, which contain columns.

#### Cassandra

For more info - [check here](https://hub.docker.com/_/cassandra).

```dockerfile
version: '2.3'
services:
  cassandra:
    image: cassandra
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9160:9160'     # Thrift client API
      - '9042:9042'     # CQL native transport port
      - '7199:7199'     # JMX
      - '7001:7001'     # TLS Internode communication
      - '7000:7000'     # Internode communication
    environment:
      CASSANDRA_START_RPC: 'true'
```

#### ClickHouse

For more info - [check here](https://hub.docker.com/r/yandex/clickhouse-server).

```dockerfile
version: '2.3'
services:
  clickhouse-server:
    image: yandex/clickhouse-server
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8123:8123'
      - '9000:9000'
      - '9009:9009'
    volumes:
      - ./clickhouse/config.xml:/etc/clickhouse-server/config.xml
```

##### ClickHouse CLI

For more info - [check here](https://hub.docker.com/r/yandex/clickhouse-client).

Depends on [ClickHouse Server](#ClickHouse).

```dockerfile
version: '2.3'
services:
  clickhouse-client:
    image: yandex/clickhouse-client
    restart: unless-stopped
#    logging:
#      driver: none
    depends_on:
      - clickhouse-server
    command: ['--host', 'clickhouse-server']
```

##### ClickHouse JDBC Bridge

For more info - [check here](https://hub.docker.com/r/riftbit/clickhouse-jdbc-bridge-service) and [here](https://github.com/ClickHouse/clickhouse-jdbc-bridge).

Depends on [ClickHouse Server](#ClickHouse).

```dockerfile
version: '2.3'
services:
  clickhouse-jdbc-bridge:
    image: riftbit/clickhouse-jdbc-bridge-service
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9019:9019'
    depends_on:
      - clickhouse-server
    volumes:
      - ./clickhouse/datasources.properties:/etc/clickhouse-jdbc-bridge/datasources.properties
```

##### Redash

Redash is an open-source data visualization tool used by companies as diverse as Soundcloud, Mozilla, and Waze. 
It allows developers and analyts to query data, graph results, and share insights with others. 
The best thing about Redash is that it is completely free to self-host.

For more info - [check here](https://hub.docker.com/r/redash/redash) and [here](https://github.com/getredash/redash/blob/master/docker-compose.yml).

Depends on [ClickHouse Server](#ClickHouse).

```dockerfile
version: '2.3'
services:
  redash-createdb:
    image: redash/redash
#    logging:
#      driver: none
    depends_on:
      - clickhouse-server
      - redis
      - postgres
    command: create_db
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: redis://redis:6379/0
      REDASH_DATABASE_URL: postgres://postgres:postgres@postgres/postgres
      REDASH_LOG_LEVEL: INFO
      REDASH_RATELIMIT_ENABLED: 'false'


  redash-server:
    image: redash/redash
    restart: unless-stopped
    logging:
      driver: none
    ports:
      - '5000:5000'
      - '5678:5678'
    depends_on:
      - clickhouse-server
      - redis
      - postgres
      - redash-createdb
    command: dev_server
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: redis://redis:6379/0
      REDASH_DATABASE_URL: postgres://postgres:postgres@postgres/postgres
      REDASH_LOG_LEVEL: INFO
      REDASH_RATELIMIT_ENABLED: 'false'


  redash-scheduler:
    image: redash/redash
    restart: unless-stopped
    logging:
      driver: none
    depends_on:
      - clickhouse-server
      - redis
      - postgres
      - redash-createdb
    command: dev_scheduler
    environment:
      REDASH_REDIS_URL: redis://redis:6379/0
      REDASH_RATELIMIT_ENABLED: 'false'


  redash-worker:
    image: redash/redash
    restart: unless-stopped
    logging:
      driver: none
    depends_on:
      - clickhouse-server
      - redis
      - postgres
      - redash-createdb
    command: dev_worker
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: redis://redis:6379/0
      REDASH_DATABASE_URL: postgres://postgres:postgres@postgres/postgres
      REDASH_LOG_LEVEL: INFO
```

### Graph Databases

A graph database is a database that uses a graphical model to represent and store the data.
The graph database model is an alternative to the relational model.
In a relational database, data is stored in tables using a rigid structure with a predefined schema.
In a graph database, there is no predefined schema as such. Rather, any schema is simply a reflection 
of the data that has been entered. As more varied data is entered, the schema grows accordingly.

#### JanusGraph

Depends on [Cassandra](#Cassandra) and [Elastic Search](#ElasticSearch).

For more info - [check here](https://hub.docker.com/r/janusgraph/janusgraph).

```dockerfile
version: '2.3'
services:
  janusgraph:
    image: janusgraph/janusgraph
    restart: unless-stopped
#    logging:
#      driver: none
    volumes:
      - ./janusgraph:/opt/janusgraph/conf
    ports:
      - '8182:8182'
    depends_on:
      - elastic
      - cassandra
    entrypoint:
      - /bin/bash
      - ./bin/gremlin-server.sh
      - /opt/janusgraph/conf/gremlin-server/gremlin-server-configuration.yaml
```

#### ArangoDB Graph

ArangoDB be used as graph, check for [more here](https://www.arangodb.com/docs/stable/aql/graphs-traversals-explained.html)
and [here](https://www.arangodb.com/arangodb-training-center/graphs/).

Docker Compose [here](#ArangoDB).

### Search Engines

Search engines or databases or services that allow fulltext and other types of search on data using 3-grams and other methods.

#### ElasticSearch

For more info - [check here](https://hub.docker.com/_/elasticsearch).

```dockerfile
version: '2.3'
services:
  elastic:
    image: elasticsearch
    restart: unless-stopped
#    logging:
#      driver: none
     ports:
       - 9200:9200
       - 9300:9300
     environment:
       discovery.type: single-node
```

#### ArangoDB Search

ArangoDB can be used as Search Engine, check for [more here](https://www.arangodb.com/arangodb-training-center/search/arangosearch/).

Docker Compose [here](#ArangoDB).

## Kafka infrastructure

Apache Kafka is a distributed streaming platform that is used to build real time streaming data pipelines 
and applications that adapt to data streams.

### Kafka

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-kafka).

Depends on [Zookeeper](#Zookeeper).

```dockerfile
version: '2.3'
services:
  kafka:
    image: confluentinc/cp-kafka
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9092:9092'
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      # Option advertised.listeners {name://host:port} used so someone can access kafka outside of container\cluster.
      # 'kafka:29092' used by clickhouse-server inside docker-compose network, when 'localhost:9092' is used by containers inside network.
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### Zookeeper

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-zookeeper).

```dockerfile
version: '2.3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper
    restart: unless-stopped
#    logging:
#      driver: none
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
```

### Kafka Topics

For more info - [check here](https://hub.docker.com/r/landoop/kafka-topics-ui).

Depends on [Kafka](#Kafka) and [Kafka REST](#Kafka REST) and [Schema Registry](#Schema Registry).

```dockerfile
version: '2.3'
services:
  kafka-topics:
    image: landoop/kafka-topics-ui
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8001:8000'
    depends_on:
      - kafka
      - kafka-rest
      - schema-registry
    environment:
      KAFKA_REST_PROXY_URL: kafka-rest:8083
      PROXY: 'true'
```

### Kafka REST

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-kafka-rest).

Depends on [Kafka](#Kafka) and [Schema Registry](#Schema Registry).

```dockerfile
version: '2.3'
services:
  kafka-rest:
    image: confluentinc/cp-kafka-rest
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8083:8083'
    depends_on:
      - kafka
      - schema-registry
    environment:
      KAFKA_REST_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_REST_LISTENERS: http://kafka-rest:8083
      KAFKA_REST_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      KAFKA_REST_HOST_NAME: kafka
```

### Schema Registry

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-schema-registry).

Depends on [Kafka](#Kafka).

```dockerfile
version: '2.3'
services:
  schema-registry:
    image: confluentinc/cp-schema-registry
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9081:8081'
    depends_on:
      - kafka
    environment:
      SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL: 'zookeeper:2181'
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_LISTENERS: http://schema-registry:8081
```


### KSQL

KSQL is the streaming SQL engine for Apache Kafka. It provides an easy-to-use yet powerful interactive SQL interface 
for stream processing on Kafka, without the need to write code in a programming language such as Java or Python.

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-ksql-server).

Depends on [Kafka](#Kafka) and [Schema Registry](#Schema Registry).

```dockerfile
version: '2.3'
services:
  ksql-server:
    image: confluentinc/cp-ksql-server
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8088:8088'
    depends_on:
      - kafka
      - schema-registry
    environment:
      KSQL_BOOTSTRAP_SERVERS: kafka:39092
      KSQL_LISTENERS: http://ksql-server:8088
      KSQL_KSQL_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      KSQL_PRODUCER_INTERCEPTOR_CLASSES: io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor
      KSQL_CONSUMER_INTERCEPTOR_CLASSES: io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor
```

#### KSQL Client

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-ksql-cli).

Depends on [KSQL Server](#KSQL).

```dockerfile
version: '2.3'
services:
  ksql-cli:
    image: confluentinc/cp-ksql-cli
    restart: unless-stopped
#    logging:
#      driver: none
    depends_on:
      - ksql-server
    entrypoint: /bin/sh
    tty: true
```

#### KSQL Control Center

For more info - [check here](https://hub.docker.com/r/confluentinc/cp-enterprise-control-center).

Depends on [Kafka](#Kafka) and [KSQL Server](#KSQL).

```dockerfile
version: '2.3'
services:
  control-center:
    image: confluentinc/cp-enterprise-control-center
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9021:9021'
    depends_on:
      - kafka
      - ksql-server
    environment:
      CONTROL_CENTER_BOOTSTRAP_SERVERS: kafka:39092
      CONTROL_CENTER_ZOOKEEPER_CONNECT: zookeeper:2181
      CONTROL_CENTER_KSQL_URL: http://ksql-server:8088
      CONTROL_CENTER_KSQL_ADVERTISED_URL: http://localhost:8088
      CONTROL_CENTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      CONTROL_CENTER_REPLICATION_FACTOR: 1
      CONTROL_CENTER_INTERNAL_TOPICS_PARTITIONS: 1
      CONTROL_CENTER_MONITORING_INTERCEPTOR_TOPIC_PARTITIONS: 1
      CONFLUENT_METRICS_TOPIC_REPLICATION: 1
      PORT: 9021
```


## Message Brokers

### NATS

For more info - [check here](https://docs.nats.io/nats-server/nats_docker).

```dockerfile
version: '2.3'
services:
  nats:
    image: nats
    logging:
      driver: none
    ports:
      - '8222:8222'
      - '4222:4222'
    hostname: nats-server
```



## Big Data

### Apache Nifi

For more info - [check here](https://hub.docker.com/r/apache/nifi).

```dockerfile
version: '2.3'
services:
  nifi:
    image: apache/nifi
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '8090:8080'
```



## Infrastructure

### Minio
```dockerfile
version: '2.3'
services:
  minio1:
    image: minio/minio
    restart: unless-stopped
#   logging:
#     driver: none
    ports:
      - '9011:9000'
    volumes:
      - /data1
      - /data2
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...2}/data{1...2}
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:9000/minio/health/live']
      interval: 30s
      timeout: 20s
      retries: 3

  minio2:
    image: minio/minio
    restart: unless-stopped
#   logging:
#     driver: none
    ports:
      - '9012:9000'
    volumes:
      - /data1
      - /data2
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server http://minio{1...2}/data{1...2}
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:9000/minio/health/live']
      interval: 30s
      timeout: 20s
      retries: 3
```

### Jaeger

For more info - [check here](https://www.jaegertracing.io/docs/1.8/getting-started/#all-in-one).

```dockerfile
version: '2.3'
services:
  jaeger:
    image: jaegertracing/all-in-one
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '6831:6831/udp'
      - '16686:16686'
```

### PACT Broker

For more info - [check here](https://hub.docker.com/r/dius/pact-broker).

Depends on [Postgres](#Postgres).

```dockerfile
version: '2.3'
services:
  pact-broker:
    image: dius/pact-broker
    restart: unless-stopped
#    logging:
#      driver: none
    links:
      - postgres
    depends_on:
      - postgres
    ports:
      - '10001:80'
    environment:
      PACT_BROKER_BASIC_AUTH_USERNAME: root
      PACT_BROKER_BASIC_AUTH_PASSWORD: 1234
      PACT_BROKER_BASIC_AUTH_READ_ONLY_USERNAME: 'true'
      PACT_BROKER_BASIC_AUTH_READ_ONLY_PASSWORD: 'true'
      PACT_BROKER_DATABASE_HOST: db
      PACT_BROKER_DATABASE_NAME: postgres
      PACT_BROKER_DATABASE_USERNAME: postgres
      PACT_BROKER_DATABASE_PASSWORD: postgres
      check_for_potential_duplicate_pacticipant_names: 'false'
```

### SonarQube

For more info - [check here](https://hub.docker.com/_/sonarqube).

```dockerfile
version: '2.3'
services:
  sonarqube:
    image: sonarqube
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '9090:9000'
```

### LiquiBase

For more info - [check here](https://hub.docker.com/r/kilna/liquibase-postgres/).

Depends on [Postgres](#Postgres).

```dockerfile
version: '2.3'
services:
  liquibase:
    image: kilna/liquibase-postgres
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '32770:5432'
    depends_on:
       - postgres
    environment:
      LIQUIBASE_HOST: postgres
      LIQUIBASE_PORT: 5432
      LIQUIBASE_DATABASE: postgres
      LIQUIBASE_USERNAME: postgres
      LIQUIBASE_PASSWORD: postgres
```

### Apache Atlas

For more info - [check here](https://github.com/ing-bank/rokku-dev-apache-atlas).

Depends on [Kafka](#Kafka).

```dockerfile
version: '2.3'
services:
  atlas-server:
    image: wbaa/rokku-dev-apache-atlas
    restart: unless-stopped
#    logging:
#      driver: none
    ports:
      - '21000:21000'
    depends_on:
      - kafka
```

### GraphQL Apollo Federation

For more info - [check here](https://www.apollographql.com/docs/federation/) and [here](https://www.javatpoint.com/graphql-apollo-server-installation).

```dockerfile
version: '2.3'
services:
  apollo-federation:
    image: xmorse/apollo-federation-gateway
    restart: unless-stopped
    logging:
      driver: none
    ports:
      - '8000:80'
    environment:
      CACHE_MAX_AGE: '5' # default cache
      ENGINE_API_KEY: '...' # to connect to the apollo engine
      POLL_INTERVAL: 30 # to update services changes
      URL_0: "http://host.docker.internal:8080/graphql"   # For local service to access or can be external link
      URL_1: "http://host.docker.internal:8081/graphql"   # For local service to access or can be external link
      # You can add any amount of services URL_2, URL_3, etc
```