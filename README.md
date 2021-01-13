# strimzi-kafka-connect
Custom Kafka-Connect-Image for Strimzi. Base-image contains

* APICURIO registry-client
* Confluent-Registry-cient
* Avro-Libraries
* Some useful 3rdparty libraries

JDBC-Image additionally contains

* Confluent jdbc-connector
* maridab, sql-server, oracle drivers


## Building images

Pass a custom maven-central-repo url as build-arg, e.g.

 docker build --build-arg MAVEN_REPO_CENTRAL=https://nexus..../repository/maven-public

## Add 3rd-Party-Libraries
3rd-party libraries/plugins need to present in a maven-repository. The artifact need to be present in tar.gz format, and has to contain a directory structure. It is possible to use artifacts/plugins from confluent-hub.
The ZIP-Archive has to be converted into a .tar.gz archive - that's all.

### Upload artifact to repository

```
mvn deploy:deploy-file \
  -DgroupId=com.github.jcustenborder \
  -DartifactId=kafka-connect-transform-common \
  -Dversion=0.1.0.36 \
  -Dpackaging=tar.gz \
  -Dfile=jcustenborder-kafka-connect-transform-common-0.1.0.36.tar.gz \
  -DrepositoryId=nexus-releases \
  -Durl=https://***/repository/maven-releases
``` 

### Download in Docker-Build
Run the following command in the build. The dots in the group-id have to be replaced by /

```
RUN docker-maven-download generic groupId artifactId version md5checksum
```

A real-life example looks like that

```
RUN docker-maven-download generic com/github/jcustenborder kafka-connect-transform-common 0.1.0.36 b20c5ae75d8b083a9a8a6d049508290d
```
