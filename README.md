# Ultimate Docker Compose for (Kafka, Zookeeper, KSQL, Postgres, MongoDB, ArangoDB, Oracle, ClickHouse, PACT, Jaeger and more)

Docker Compose for all services that I was working with or configured while developing.

Just in case I need to ready-n-run them all so I can do it from one place.
Or as a builder with configured docker compose dependencies.

- [Databases:](#Databases)
    - [RDBMS:](#RDBMS)
        - [Postgres:](#Postgres)
            - [PG Admin 4](#Postgres Admin)
        - [Oracle](#Oracle)
        - [Cockroach](#Cockroach)
    - [Document Oriented:](#Document Oriented)
        - [MongoDB](#MongoDB)
        - [ArangoDB](#ArangoDB)
    - [Key-Value:](#Key-Value)
        - [Redis](#Redis)
            - [CLI](#Redis CLI)
        - [Infinispan](#Infinispan)
        - [Hazelcast](#Hazelcast)
    - [Column-oriented DBMS:](#Column Oriented DBMS)
        - [Cassandra](#Cassandra)
        - [ClickHouse:](#ClickHouse)
            - [CLI](#ClickHouse CLI)
            - [JDBC-Bridge](#ClickHouse JDBC Bridge)
            - [Redash](#Redash)
    - [Graph Databases:](#Graph Databases)
        - [JanusGraph](#JanusGraph)
        - [ArangoDB](#ArangoDB Graph)
    - [Search Engines:](#Search Engines)
        - [ElasticSearch](#ElasticSearch)
        - [ArangoDB](#ArangoDB Search)
    
- [Kafka infrastructure:](#Kafka infrastructure)
    - [Kafka](#Kafka)
    - [Zookeeper](#Zookeeper)
    - [Kafka Topics](#Kafka Topics)
    - [Kafka Rest](#Kafka Rest)
    - [KSQL:](#KSQL)
        - [Client](#KSQL Client)
        - [Control Center](#KSQL Control Center)
        
- [Big Data:](#Big Data)
    - [Apache Nifi](#Apache Nifi)
    
- [Infrastructure:](#Infrastructure)
    - [Jaeger](#Jaeger)
    - [PACT Broker](#PACT Broker)
    - [SonarQube](#SonarQube)
    - [LiquiBase](#LiquiBase)
    - [Apache Atlas](#Apache Atlas)
    - [Schema Registry](#Schema Registry)
    
    
## Databases

### RDBMS

RDBMS stands for Relational Database Management System. 
RDBMS is the basis for SQL, and for all modern database systems like Postgres, Oracle, MySQL and etc.

#### Postgres
```dockerfile
version: '2.3'
services:
  postgres:
    image: postgres
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
```dockerfile
version: '2.3'
services:
  pgadmin4:
    image: dpage/pgadmin4
#    logging:
#      driver: none
    ports:
      - '8010:80'
    environment:
      - PGADMIN_DEFAULT_EMAIL=bob@gmail.com
      - PGADMIN_DEFAULT_PASSWORD=postgres
```

#### Oracle
```dockerfile
version: '2.3'
services:
  oracle:
    image: store/oracle/database-enterprise:12.2.0.1
#    logging:
#      driver: none
    ports:
    - '1521:1521'
    - '5500:5500'
```

#### Cockroach
```dockerfile
version: '2.3'
services:
  cockroach:
    image: cockroachdb/cockroach
#    logging:
#      driver: none
    command: start --insecure
    ports:
      - '26257:26257'
      - '10000:8080'
```

### Document Oriented
```dockerfile
version: '2.3'
services:
  pgadmin4:
    image: dpage/pgadmin4
#    logging:
#      driver: none
    ports:
      - '8010:80'
    environment:
      - PGADMIN_DEFAULT_EMAIL=bob@gmail.com
      - PGADMIN_DEFAULT_PASSWORD=postgres
```

#### MongoDB
```dockerfile
version: '2.3'
services:
  mongo:
    image: mongo
#    logging:
#      driver: none
    ports:
      - '27017:27017'
    environment:
      - MONGO_INITDB_ROOT_USERNAME=mongo
      - MONGO_INITDB_ROOT_PASSWORD=mongo
      - MONGO_INITDB_DATABASE=public
```

#### ArangoDB
```dockerfile
version: '2.3'
services:
  arangodb:
    image: arangodb
#    logging:
#      driver: none
    ports:
      - '8529:8529'
    environment:
      - ARANGO_NO_AUTH=1
```

### Key-Value (Cache)

#### Redis
```dockerfile
version: '2.3'
services:
  redis:
    image: redis
#    logging:
#      driver: none
    ports:
      - '6379:6379'
    command: redis-server --requirepass test
```

##### Redis CLI
```dockerfile
version: '2.3'
services:
  redis-commander:
    image: rediscommander/redis-commander
    logging:
      driver: none
    ports:
      - '9200:8081'
    environment:
      - REDIS_HOSTS=local:redis:6379:0:test
```

#### Infinispan
```dockerfile
version: '2.3'
services:
  infinispan:
    image: infinispan/server
#    logging:
#      driver: none
    ports:
      - '11222:11222'
    environment:
      - USER=test
      - PASS=test
```

#### Hazelcast
```dockerfile
version: '2.3'
services:
  hazelcast:
    image: hazelcast/hazelcast
#    logging:
#      driver: none
    ports:
      - '5701:5701'
    environment:
      - JAVA_OPTS=-Dhazelcast.shutdownhook.policy=GRACEFUL
```

### Column Oriented DBMS

#### Cassandra
```dockerfile
version: '2.3'
services:
  cassandra:
    image: cassandra
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
```dockerfile
version: '2.3'
services:
  clickhouse-server:
    image: yandex/clickhouse-server
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
```dockerfile
version: '2.3'
services:
  clickhouse-client:
    image: yandex/clickhouse-client
#    logging:
#      driver: none
    depends_on:
      - clickhouse-server
    command: ['--host', 'clickhouse-server']
```

##### ClickHouse JDBC Bridge
```dockerfile
version: '2.3'
services:
  clickhouse-jdbc-bridge:
    image: riftbit/clickhouse-jdbc-bridge-service
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
```dockerfile
version: '2.3'
services:
  redash-createdb:
    image: redash/redash
#    logging:
#      driver: none
    depends_on:
      - redis
      - postgres
    command: create_db
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgres://postgres:postgres@postgres/postgres"
      REDASH_LOG_LEVEL: "INFO"
      REDASH_RATELIMIT_ENABLED: "false"


  redash-server:
    image: redash/redash
    logging:
      driver: none
    ports:
      - '5000:5000'
      - '5678:5678'
    depends_on:
      - redis
      - postgres
      - redash-createdb
    command: dev_server
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgres://postgres:postgres@postgres/postgres"
      REDASH_LOG_LEVEL: "INFO"
      REDASH_RATELIMIT_ENABLED: "false"


  redash-scheduler:
    image: redash/redash
    logging:
      driver: none
    depends_on:
      - redis
      - postgres
      - redash-createdb
    command: dev_scheduler
    environment:
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_RATELIMIT_ENABLED: "false"


  redash-worker:
    image: redash/redash
    logging:
      driver: none
    depends_on:
      - redis
      - postgres
      - redash-createdb
    command: dev_worker
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgres://postgres:postgres@postgres/postgres"
      REDASH_LOG_LEVEL: "INFO"
```

### Graph Databases

#### JanusGraph
```dockerfile
version: '2.3'
services:
  janusgraph:
    image: janusgraph/janusgraph
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



### Search Engines

#### ElasticSearch
```dockerfile
version: '2.3'
services:
  elastic:
     image: elasticsearch
#    logging:
#      driver: none
     ports:
       - 9200:9200
       - 9300:9300
     environment:
       discovery.type: single-node
```

#### ArangoDB Search



## Kafka infrastructure

### Kafka
```dockerfile
version: '2.3'
services:
  kafka:
    image: confluentinc/cp-kafka
    logging:
      driver: none
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
```dockerfile
version: '2.3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper
    logging:
      driver: none
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
```

### Kafka Topics
```dockerfile
version: '2.3'
services:
  kafka-topics:
    image: landoop/kafka-topics-ui
    logging:
      driver: none
    ports:
      - '8001:8000'
    depends_on:
      - zookeeper
      - kafka
      - kafka-rest
      - schema-registry
    environment:
      KAFKA_REST_PROXY_URL: 'kafka-rest:8083'
      PROXY: 'true'
```

### Kafka Rest
```dockerfile
version: '2.3'
services:
  kafka-rest:
    image: confluentinc/cp-kafka-rest
    logging:
      driver: none
    ports:
      - '8083:8083'
    depends_on:
      - zookeeper
      - kafka
      - schema-registry
    environment:
      KAFKA_REST_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_REST_LISTENERS: 'http://kafka-rest:8083'
      KAFKA_REST_SCHEMA_REGISTRY_URL: 'http://schema-registry:8081'
      KAFKA_REST_HOST_NAME: kafka
```


### KSQL
```dockerfile
version: '2.3'
services:
  ksql-server:
    image: confluentinc/cp-ksql-server
#    logging:
#      driver: none
    ports:
      - '8088:8088'
    depends_on:
      - kafka
      - schema-registry
      - zookeeper
    environment:
      KSQL_BOOTSTRAP_SERVERS: kafka:39092
      KSQL_LISTENERS: http://ksql-server:8088
      KSQL_KSQL_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      KSQL_PRODUCER_INTERCEPTOR_CLASSES: "io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor"
      KSQL_CONSUMER_INTERCEPTOR_CLASSES: "io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor"
```

#### KSQL Client
```dockerfile
version: '2.3'
services:
  ksql-cli:
    image: confluentinc/cp-ksql-cli:5.3.0
    logging:
      driver: none
    depends_on:
      - kafka
      - ksql-server
    entrypoint: /bin/sh
    tty: true
```

#### KSQL Control Center
```dockerfile
version: '2.3'
services:
  control-center:
    image: confluentinc/cp-enterprise-control-center:5.3.0
    logging:
      driver: none
    ports:
      - '9021:9021'
    depends_on:
      - zookeeper
      - kafka
      - schema-registry
      - ksql-server
    environment:
      CONTROL_CENTER_BOOTSTRAP_SERVERS: 'kafka:39092'
      CONTROL_CENTER_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      CONTROL_CENTER_KSQL_URL: "http://ksql-server:8088"
      CONTROL_CENTER_KSQL_ADVERTISED_URL: "http://localhost:8088"
      CONTROL_CENTER_SCHEMA_REGISTRY_URL: "http://schema-registry:8081"
      CONTROL_CENTER_REPLICATION_FACTOR: 1
      CONTROL_CENTER_INTERNAL_TOPICS_PARTITIONS: 1
      CONTROL_CENTER_MONITORING_INTERCEPTOR_TOPIC_PARTITIONS: 1
      CONFLUENT_METRICS_TOPIC_REPLICATION: 1
      PORT: 9021
```



## Big Data

### Apache Nifi
```dockerfile
version: '2.3'
services:
  nifi:
    image: apache/nifi
    logging:
      driver: none
    ports:
      - '9090:8080'
```



## Infrastructure

### Jaeger
```dockerfile
version: '2.3'
services:
  jaeger:
    image: jaegertracing/all-in-one
    logging:
      driver: none
    ports:
      - '6831:6831/udp'
      - '16686:16686'
```

### PACT Broker
```dockerfile
version: '2.3'
services:
  pact-broker:
    image: dius/pact-broker
    logging:
      driver: none
    links:
      - postgres
    depends_on:
      - postgres
    ports:
      - '10001:80'
    environment:
      PACT_BROKER_BASIC_AUTH_USERNAME: 'root'
      PACT_BROKER_BASIC_AUTH_PASSWORD: '1234'
      PACT_BROKER_BASIC_AUTH_READ_ONLY_USERNAME: 'true'
      PACT_BROKER_BASIC_AUTH_READ_ONLY_PASSWORD: 'true'
      PACT_BROKER_DATABASE_HOST: db
      PACT_BROKER_DATABASE_NAME: postgres
      PACT_BROKER_DATABASE_USERNAME: postgres
      PACT_BROKER_DATABASE_PASSWORD: postgres
      check_for_potential_duplicate_pacticipant_names: 'false'
```

### SonarQube
```dockerfile
version: '2.3'
services:
  sonarqube:
    logging:
      driver: none
    image: sonarqube
    ports:
      - '9000:9000'
```

### LiquiBase
```dockerfile
version: '2.3'
services:
  liquibase:
    image: postgres
    logging:
      driver: none
    restart: always
    ports:
      - '32770:5432'
    depends_on:
       - postgres
    environment:
      postgres_db: postgres
      postgres_user: postgres
      postgres_password: postgres
```

### Apache Atlas
```dockerfile
version: '2.3'
services:
  atlas-server:
    image: wbaa/rokku-dev-apache-atlas
    logging:
      driver: none
    ports:
      - '21000:21000'
    depends_on:
      - zookeeper
      - kafka
```

### Schema Registry
```dockerfile
version: '2.3'
services:
  schema-registry:
    image: confluentinc/cp-schema-registry
    logging:
      driver: none
    ports:
      - '9081:8081'
    depends_on:
      - zookeeper
      - kafka
    environment:
      SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL: 'zookeeper:2181'
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_LISTENERS: 'http://schema-registry:8081'
```
