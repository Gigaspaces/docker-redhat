FROM registry.access.redhat.com/rhel7/rhel

LABEL name="insightedge-enterprise-rhel-v14-0"
LABEL vendor="GigaSpaces Technologies Inc"
LABEL version="14.0.1"
LABEL release="v2"
LABEL summary="Real-Time Analytics Platform"
LABEL description="The InsightEdge Platform is a first-class integration between Apache Spark and the XAP data grid. This integration co-locates Spark jobs with the data they need to access, providing ultra-low latency and high-throughput transaction and stream processing. InsightEdge simplifies and streamlines business optimization, enabling real-time decision support and automated intelligence through unifying fast-data analytics, AI and real-time applications."

ARG RH_user=yaelk@gigaspaces.com
ARG RH_password=pashpo36
ARG GS_PRODUCT=insightedge
ARG GS_VERSION=14.0.0
ARG GS_MILESTONE=ga
ARG GS_BUILD=20000
ARG GS_NAME=gigaspaces-${GS_PRODUCT}-enterprise-${GS_VERSION}-${GS_MILESTONE}-b${GS_BUILD}
ARG GS_URL=https://gigaspaces-releases-eu.s3.amazonaws.com/${GS_PRODUCT}/${GS_VERSION}/${GS_NAME}.zip

ENV GS_HOME /opt/gigaspaces
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

# Configure and expose ports
ENV XAP_NIC_ADDRESS "#eth0:ip#"
ENV XAP_LOOKUP_PORT 4174
ENV XAP_LRMI_PORT 8200-8300
ENV XAP_MANAGER_REST_PORT 8090
ENV WEBUI_PORT 8099
ENV XAP_WEBSTER_HTTP_PORT 8199
ENV XAP_RMI_REGISTRY_PORT 10098
ENV XAP_REST_PORT 8089
ENV XAP_ZOOKEEPER_CLIENT_PORT 2181
ENV XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT 2888
ENV XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT 3888
ENV SPARK_MASTER_PORT 7077
ENV SPARK_MASTER_WEBUI_PORT 8080
ENV SPARK_MASTER_REST_PORT 6066
ENV ZEPPELIN_PORT 9090
EXPOSE ${XAP_LOOKUP_PORT} ${XAP_LRMI_PORT} ${XAP_MANAGER_REST_PORT} ${WEBUI_PORT} ${XAP_WEBSTER_HTTP_PORT} ${XAP_RMI_REGISTRY_PORT} ${XAP_REST_PORT} ${XAP_ZOOKEEPER_CLIENT_PORT} ${XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT}  ${XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT}   ${SPARK_MASTER_PORT} ${SPARK_MASTER_WEBUI_PORT} ${SPARK_MASTER_REST_PORT} ${ZEPPELIN_PORT}
ENV EXT_JAVA_OPTIONS "-Dcom.gs.multicast.discoveryPort=${XAP_LOOKUP_PORT} -Dcom.gs.transport_protocol.lrmi.bind-port=${XAP_LRMI_PORT} -Dcom.gs.manager.rest.port=${XAP_MANAGER_REST_PORT} -Dorg.openspaces.launcher.port=${WEBUI_PORT} -Dcom.gigaspaces.start.httpPort=${XAP_WEBSTER_HTTP_PORT} -Dcom.gigaspaces.start.rmi.registryPort=${XAP_RMI_REGISTRY_PORT}  -Dcom.gs.manager.zookeeper.discovery.port=${XAP_MANAGER_ZOOKEEPER_DISCOVERY_PORT} -Dcom.gs.manager.zookeeper.leader-election.port=${XAP_MANAGER_ZOOKEEPER_LEADER_ELECTION_PORT} -Dzeppelin.server.port=${ZEPPELIN_PORT}"

# Configure Spark directory structure
ENV SPARK_HOME ${GS_HOME}/insightedge/spark

RUN mkdir -p ${SPARK_HOME} && \
    mkdir -p ${SPARK_HOME}/work-dir && \
    touch ${SPARK_HOME}/RELEASE && \
    rm /bin/sh && \
    ln -sv /bin/bash /bin/sh && \
    chgrp root /etc/passwd && chmod ug+rw /etc/passwd

# Configure Classpath environment variables
ENV INSIGHTEDGE_CLASSPATH "${GS_HOME}/insightedge/lib/*:${GS_HOME}/lib/required/*:${GS_HOME}/lib/optional/spatial/*"
ENV SPARK_CLASSPATH "${SPARK_CLASSPATH}:${INSIGHTEDGE_CLASSPATH}"

# Configure InsightEdge application's environment variables
ENV INSIGHTEDGE_SPACE_NAME insightedge-space

# Add entrypoints to directory structure
ADD docker-entrypoint.sh /opt/docker-entrypoint.sh
ADD entrypoint.sh /opt/entrypoint.sh
RUN cp /opt/gigaspaces/insightedge/spark/kubernetes/dockerfiles/spark/entrypoint.sh /opt/spark_entrypoint.sh

# Configure Docker entrypoint
ENTRYPOINT [ "/opt/docker-entrypoint.sh" ]
CMD ["host", "run-agent", "--auto"]