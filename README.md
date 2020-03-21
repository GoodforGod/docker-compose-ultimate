# Ultimate Docker Compose for (Kafka, Zookeeper, KSQL, Postgres, MongoDB, ArangoDB, Oracle, ClickHouse, PACT, Jaeger and more)

Docker Compose for all services that I was working with or configured while developing.

Just in case I need to ready-n-run them all so I can do it from one place.
Or as a builder with configured docker compose dependencies.

- Databases:
    - RDBMS:
        - Postgres:
            - Database
            - Admin
        - Oracle
        - Cockroach
    - Document Oriented:
        - MongoDB
        - ArangoDB
    - Key-Value (Cache):
        - Redis:
            - Database
            - CLI
        - Infinispan
        - Hazelcast
    - Column-oriented DBMS:
        - Cassandra
        - ClickHouse:
            - Database
            - CLI
            - JDBC-Bridge
            - Redash
    - Graph Databases:
        - ArangoDB
    - Search Engines:
        - ElasticSearch
        - ArangoDB
    
- Kafka infrastructure:
    - Kafka
    - Zookeeper
    - Kafka Topics
    - Kafka Rest
    - Schema Registry
    - KSQL:
        - Server
        - Client
        - Control Center
        
- Big Data:
    - Apache Nifi
    
- Infrastructure:
    - Jaeger (All in One)
    - PACT Broker
    - SonarQube
    - LiquiBase
    - Apache Atlas
    
    
## Databases
### RDBMS
#### Postgres
##### Database
##### Admin
#### Oracle
#### Cockroach
### Document Oriented
#### MongoDB
#### ArangoDB
### Key-Value (Cache)
#### Redis:
##### Database
##### CLI
#### Infinispan
#### Hazelcast
### Column-oriented DBMS
#### Cassandra
#### ClickHouse
##### Database
##### CLI
##### JDBC-Bridge
##### Redash
### Graph Databases
#### ArangoDB
### Search Engines
#### ElasticSearch
#### ArangoDB

## Kafka infrastructure
### Kafka
### Zookeeper
### Kafka Topics
### Kafka Rest
### Schema Registry
### KSQL:
#### Server
#### Client
#### Control Center

## Big Data
### Apache Nifi

## Infrastructure
### Jaeger (All in One)
### PACT Broker
### SonarQube
### LiquiBase
### Apache Atlas