FROM strimzi-kafka-connect-base:0.0.2
USER root:root

ARG MAVEN_REPO_CENTRAL=""

ENV MAVEN_REPO_CENTRAL=$MAVEN_REPO_CENTRAL \
    MAVEN_REPOS_ADDITIONAL="" \
    MAVEN_DEP_DESTINATION="$KAFKA_CONNECT_PLUGINS_DIR/jdbc" \
    CONNECT_JDBC_MD5=ea632e0956792055f220539d24a353b8 \
    MARIADB_MD5=4935627c539ea79061683b29c4904387 \
    POSTGRES_MD5=a57d653d21c01646bc7d2611d84bc268 \
    SQLSERVER_MD5=c7c65f503177664504e24ea4fa231a9a \
    ORACLE_MD5=03efd5a0799c4cc0f043c8ee6a83940d \
    DB2_MD5=23dd3705bef05d415b26144be49d30d7 \
    VITESS_MD5=6c4b93fc4d9088d30dbb59f9f054ff5e \
    SCRIPTING_MD5=826a9c60104d2d58fe983c38b0a4d05b

RUN mkdir $MAVEN_DEP_DESTINATION

RUN docker-maven-download confluent kafka-connect-jdbc "$CONFLUENT_VERSION" "$CONNECT_JDBC_MD5" && \
    docker-maven-download central org/mariadb/jdbc mariadb-java-client "2.1.0" "$MARIADB_MD5" && \
    docker-maven-download central com/microsoft/sqlserver mssql-jdbc "7.2.1.jre11" "$SQLSERVER_MD5" && \
    docker-maven-download central com/oracle ojdbc14 "12.1.0.2.0" "$ORACLE_MD5"

USER 1001
