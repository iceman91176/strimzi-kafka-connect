# strimzi-kafka-connect
Custom Kafka-Connect-Image for Strimzi. Base-image contains

* APICURIO registry-client
* Confluent-Registry-cient
* Avro-Libraries

JDBC-Image additionally contains

* Confluent jdbc-connector
* maridab, sql-server, oracle drivers


##Building images

Pass a custom maven-central-repo url as build-arg, e.g.

 docker build --build-arg MAVEN_REPO_CENTRAL=https://nexus..../repository/maven-public


