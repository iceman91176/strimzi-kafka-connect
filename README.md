# DEPRECATED AND UNMAINTAINED

This is not needed any more since strimzi is able to build the image itself -> https://strimzi.io/blog/2021/03/29/connector-build/

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

## Build custom strimzi-image
If a newer kafka-connect release is required than strimzi.io provides. It is possible to build a custom image like that. Be careful - it only works within a release-train. Don't try going from 2.6 to 2.7 

### Requirements
See https://github.com/strimzi/strimzi-kafka-operator/blob/master/development-docs/DEV_GUIDE.md#build-pre-requisites

You **don't** need

* helm
* kubernetes/openshift
* asciidoctor

Be sure to use **v3** of the **right** yq-project 

### Download the strimzi-release
Download & unpack the release where you are basing the custom image on, eg 

```
BASE_RELEASE=0.21.1
wget https://github.com/strimzi/strimzi-kafka-operator/archive/$BASE_RELEASE.tar.gz
tar -zxf $BASE_RELEASE.tar.gz
cd strimzi-kafka-operator-$BASE_RELEASE
```

### Add custom-kafka release
Add the kafka-version to kafka-versions.yaml

```
- version: 2.6.1
  format: 2.6
  protocol: 2.6
  url: https://archive.apache.org/dist/kafka/2.6.1/kafka_2.12-2.6.1.tgz
  checksum: 105BF29E4BED9F1B7A7A3CAADF016DF8AA774F6F509E3606529F8231EA4CAB89C38236C58BE9C2E9BD3C52C2917892EA5D3A5EC0BD94BD8A5F7257522A5AF4DB
  zookeeper: 3.5.8
  third-party-libs: 2.6.x
  default: true
```

You can remove the other versions, since it only increases the build-time.

### Modify-build script
The default build-script expects the whole dev-ecosystem because it wants to build strimzi-operator. Since we don't want that we have to slightly modify the build-script in **docker-images/build.sh**

Locate the lines that state

```
java_images="operator jmxtrans"
kafka_images="kafka test-client"
```

and change them to

```
java_images=""
kafka_images="kafka"
```

### Run build, tag & push

```
cd kafka-agent && make & cd ..
cd tracing-agent && make & cd ..
cd mirror-maker-agent && make & cd ..
```

Change to ./docker-images

Run build script. If you use podman, it can help to explicitely use docker-format

```
DOCKER_CMD="podman" DOCKER_BUILD_ARGS="--format docker" ./build.sh docker_build
```

Tag it with your regitry, org and the version

```
DOCKER_REGISTRY=my-registry DOCKER_ORG=strimzi DOCKER_TAG=$BASE_RELEASE ./build.sh docker_tag_default
```
This will create the following tag

* my-registry/strimzi/kafka:0.20.1-kafka-2.6.1

Push as usual.


