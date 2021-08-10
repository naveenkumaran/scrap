## Workstation setup

For the unlucky ones who do not have the privilege to run docker on their workstation, use this approach to run APICURIO registry as an executable jar. 


## Pre-requisite. 

* Start Zookeeper and Kafka 

## Execute jar 

```
java -jar apicurio-registry-storage-kafkasql-2.1.0-SNAPSHOT-runner.jar
```

The jar file creates a H2 database to manage the state of the registry backed up by kafka topic with the name kafka-journal and 