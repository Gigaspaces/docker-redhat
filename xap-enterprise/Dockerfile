FROM registry.access.redhat.com/rhel7/rhel

LABEL name="xap-enterprise-rhel-v14-0"
LABEL vendor="GigaSpaces Technologies Inc"
LABEL version="14.0.1"
LABEL release="v1"
LABEL summary="In-Memory Data Grid"
LABEL description="XAP is the core in-memory data grid for the InsightEdge Platform. XAP is designed to support ultra-low latency and high-throughput applications that follow Space-Based Architecture design patterns. XAP is based on GigaSpaces' Spring-based OpenSpaces Framework as the primary development environment, and uses the GigaSpaces space-based runtime to deliver core middleware facilities: messaging, data caching and parallelization."

ARG RH_user="redhat user"
ARG RH_password="redhat password"
ARG GS_PRODUCT=xap
ARG GS_VERSION=14.0.1
ARG GS_MILESTONE=ga
ARG GS_BUILD=20100
ARG GS_NAME=gigaspaces-${GS_PRODUCT}-enterprise-${GS_VERSION}-${GS_MILESTONE}-b${GS_BUILD}
ARG GS_URL=https://gigaspaces-releases-eu.s3.amazonaws.com/${GS_PRODUCT}/${GS_VERSION}/${GS_NAME}.zip
ENV GS_HOME /opt/gigaspaces
ENV JAVA_HOME /usr/java
WORKDIR ${GS_HOME}

# the terms_and_conditions.txt is requested by Red Hat
RUN mkdir -p /licenses
COPY terms_and_conditions.txt /licenses

# Install wget and unzip
RUN subscription-manager register --force --username ${RH_user} --password ${RH_password} --auto-attach
RUN yum install wget --assumeyes
RUN yum install unzip --assumeyes

# Download $GS_URL and unzip to $GS_HOME
RUN wget --progress=bar:force -O /tmp/gigaspaces.zip ${GS_URL} &&\
    unzip -q /tmp/gigaspaces.zip -d /tmp &&\
    mkdir -p ${GS_HOME} &&\
    mv /tmp/${GS_NAME}/* ${GS_HOME}/ &&\
    rm -f /tmp/gigaspaces.zip
ADD entrypoint.sh /opt/entrypoint.sh

# Download $JAVA and untar to $JAVA_HOME
RUN wget --progress=bar:force -O /tmp/java.tar.gz https://download.java.net/java/ga/jdk11/openjdk-11_linux-x64_bin.tar.gz &&\
    tar xC /tmp -f /tmp/java.tar.gz &&\
    mkdir -p ${JAVA_HOME} &&\
    mv /tmp/jdk-11 ${JAVA_HOME}/ &&\
    rm -f /tmp/java.tar.gz

# Configure and expose ports
ENV XAP_NIC_ADDRESS "#eth0:ip#"
ENV XAP_LOOKUP_PORT 4174
ENV XAP_LRMI_PORT 8200-8300
ENV XAP_MANAGER_REST_PORT 8090
ENV WEBUI_PORT 8099
ENV XAP_WEBSTER_HTTP_PORT 8199
ENV XAP_RMI_REGISTRY_PORT 10098
ENV XAP_REST_PORT 8089
##can't overwrite zookeeper_client_port
ENV XAP_ZOOKEEPER_CLIENT_PORT 2181
ENV XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT=2888
ENV XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT=3888
EXPOSE ${XAP_LOOKUP_PORT} ${XAP_LRMI_PORT} ${WEBUI_PORT} ${XAP_MANAGER_REST_PORT} ${WEBUI_PORT} ${XAP_WEBSTER_HTTP_PORT} ${XAP_RMI_REGISTRY_PORT} ${XAP_REST_PORT} ${XAP_ZOOKEEPER_CLIENT_PORT} ${XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT}  ${XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT}
ENV EXT_JAVA_OPTIONS "-Dcom.gs.multicast.discoveryPort=${XAP_LOOKUP_PORT} -Dcom.gs.transport_protocol.lrmi.bind-port=${XAP_LRMI_PORT} -Dcom.gs.manager.rest.port=${XAP_MANAGER_REST_PORT} -Dorg.openspaces.launcher.port=${WEBUI_PORT} -Dcom.gigaspaces.start.httpPort=${XAP_WEBSTER_HTTP_PORT} -Dcom.gigaspaces.start.rmi.registryPort=${XAP_RMI_REGISTRY_PORT}  -Dcom.gs.manager.zookeeper.discovery.port=${XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT} -Dcom.gs.manager.zookeeper.leader-election.port=${XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT}"
ENTRYPOINT ["/opt/entrypoint.sh"]
CMD ["host", "run-agent", "--auto"]