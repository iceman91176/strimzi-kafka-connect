FROM pub-registry.dev.witcom.services/strimzi/kafka:0.20.1-kafka-2.6.1
USER root:root

ARG MAVEN_REPO_CENTRAL=""

ENV MAVEN_REPO_CENTRAL=$MAVEN_REPO_CENTRAL \
    KAFKA_CONNECT_PLUGINS_DIR=$KAFKA_HOME/plugins \
    EXTERNAL_LIBS_DIR=$KAFKA_HOME/external_libs \
    CONNECT_PLUGIN_PATH=$AFKA_CONNECT_PLUGINS_DIR \
    MAVEN_DEP_DESTINATION=$KAFKA_HOME/libs \
    CONFLUENT_VERSION=5.5.1 \
    AVRO_VERSION=1.10.1 \
    AVRO_JACKSON_VERSION=2.10.2 \
    APICURIO_VERSION=1.3.2.Final

RUN mkdir "$KAFKA_CONNECT_PLUGINS_DIR" "$EXTERNAL_LIBS_DIR"

COPY docker-maven-download.sh /usr/local/bin/docker-maven-download

RUN docker-maven-download confluent kafka-connect-avro-converter "$CONFLUENT_VERSION" 9c1846872e6706166b7f2d7ae3922d3e && \
    docker-maven-download confluent kafka-connect-avro-data "$CONFLUENT_VERSION" 08579b13feb7421de5a7774fd16223db && \
    docker-maven-download confluent kafka-avro-serializer "$CONFLUENT_VERSION" 6f3673e5d6028136bf7e206f42aecb59 && \
    docker-maven-download confluent kafka-schema-serializer "$CONFLUENT_VERSION" 0f8db6e28b0f21aa999ec72ca50c0192 && \
    docker-maven-download confluent kafka-schema-registry-client "$CONFLUENT_VERSION" fdf60e3774342726dafddeb33962dfde && \
    docker-maven-download confluent common-config "$CONFLUENT_VERSION" 73d3339e57b1cd0c433daf98f9cb2c88 && \
    docker-maven-download confluent common-utils "$CONFLUENT_VERSION" b837b31f144799698c53c93e3ac82bba && \
    docker-maven-download central com/fasterxml/jackson/core jackson-core "$AVRO_JACKSON_VERSION" 5514a46e38331f8c8262ea63bf36483e && \
    docker-maven-download central com/fasterxml/jackson/core jackson-databind "$AVRO_JACKSON_VERSION" 057751b4e2dd1104be8caad6e9a3e589 && \
    docker-maven-download central com/fasterxml/jackson/core jackson-annotations "$AVRO_JACKSON_VERSION" d9e46501509b1751d64c495ab088a6cf && \
    docker-maven-download central org/apache/avro avro "$AVRO_VERSION" 35469fee6d74ecbadce4773bfe3a204c && \
    docker-maven-download apicurio "$APICURIO_VERSION" a25a604ae3e194bc5deba0040aae2a98

USER 1001
