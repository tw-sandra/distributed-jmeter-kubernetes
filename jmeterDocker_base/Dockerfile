FROM openjdk:16-jdk-alpine3.13
MAINTAINER sandrajohn
		
ARG JMETER_VERSION=5.4.1
ENV JMETER_INSTALLATION_PATH="/opt/jmeter/apache-jmeter-${JMETER_VERSION}"
ARG CURL_OPTS="--connect-timeout 10     --retry 5     --retry-delay 1     --retry-max-time 60"
ARG JMETER_CMD_RUNNER_PATH="${JMETER_INSTALLATION_PATH}/lib/cmdrunner-2.2.jar"
ARG JMETER_CMD_RUNNER_URL="https://repo1.maven.org/maven2/kg/apc/cmdrunner/2.2/cmdrunner-2.2.jar"
ARG JMETER_PLUGIN_URL="https://jmeter-plugins.org/get/"
ARG JMETER_PLUGIN_PATH="${JMETER_INSTALLATION_PATH}/lib/ext/jmeter-plugin-manager-1.6.jar"


RUN apk add --no-cache unzip
RUN apk --no-cache add --update curl
RUN apk add --no-cache wget
RUN apk add --no-cache bash
RUN rm -rf /var/cache/apk/*
		

RUN mkdir /opt/jmeter
RUN cd /opt/jmeter
COPY apache-jmeter-${JMETER_VERSION}.zip apache-jmeter-${JMETER_VERSION}.zip
RUN unzip apache-jmeter-${JMETER_VERSION}.zip -d /opt/jmeter
RUN rm apache-jmeter-${JMETER_VERSION}.zip
ENV PATH $PATH:$JMETER_BIN


RUN ln -s ${JMETER_INSTALLATION_PATH} /opt/jmeter/apache-jmeter
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter-master.out
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter-master.err
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter-injector.out
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter-injector.err
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter.log
RUN ln -s /proc/1/fd/1 ${JMETER_INSTALLATION_PATH}/bin/jmeter-server.log
RUN echo "client.tries=3" >> ${JMETER_INSTALLATION_PATH}/bin/jmeter.properties
RUN echo "client.retries_delay=20" >> ${JMETER_INSTALLATION_PATH}/bin/jmeter.properties

RUN curl ${CURL_OPTS} --location --output "${JMETER_PLUGIN_PATH}" -k "${JMETER_PLUGIN_URL}"
RUN curl ${CURL_OPTS} --location --output "${JMETER_CMD_RUNNER_PATH}" -k "${JMETER_CMD_RUNNER_URL}"
RUN java -classpath "${JMETER_PLUGIN_PATH}" org.jmeterplugins.repository.PluginManagerCMDInstaller

RUN curl ${CURL_OPTS} --location --output "${JMETER_INSTALLATION_PATH}/lib/ext/pepper-box-1.0.jar" -k "https://github.com/raladev/load/blob/master/JARs/pepper-box-1.0.jar?raw=true"

RUN curl ${CURL_OPTS} --location --output "${JMETER_INSTALLATION_PATH}/lib/ext/jmeter-prometheus-listener-2.2.0.jar" -k "https://github.com/kolesnikovm/jmeter-prometheus-listener/releases/download/2.2.0/jmeter-prometheus-listener-2.2.0.jar?raw=true"

#RUN cd /jmeter/apache-jmeter-${JMETER_VERSION}/
RUN curl ${CURL_OPTS} --location --output "/tmp/bzm-parallel-0.7.zip" -k "https://jmeter-plugins.org/files/packages/bzm-parallel-0.7.zip"
RUN unzip -n /tmp/bzm-parallel-0.7.zip -d ${JMETER_INSTALLATION_PATH}/lib/ext/
RUN rm /tmp/bzm-parallel-0.7.zip

RUN ${JMETER_INSTALLATION_PATH}/bin/PluginsManagerCMD.sh install-all-except ulp-jmeter-gwt-plugin, ulp-jmeter-videostreaming-plugin, ulp-jmeter-autocorrelator-plugin

ENV JMETER_HOME /opt/jmeter/apache-jmeter-${JMETER_VERSION}/
		
ENV PATH ${JMETER_HOME}bin:$PATH
