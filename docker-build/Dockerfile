# Dockerfile for fluentd sidecar image with all the necessary plugins for our log analytic providers
FROM reg.echohq.com/fluentd:1.19.2
LABEL maintainer="Partner Engineering <partner_support@jfrog.com>"

## Build time Arguments, short circuit them to ENV Variables so they are available at run time also
ARG SOURCE=JFRT
ARG TARGET=SPLUNK

## Environment Variables set by this docker file, there will be seperate env params set by a env file while running the containers
## For better maintainability always depend dockerfile code on the environment variables declared in this file to add more platforms
ENV SRC_PLATFORM=$SOURCE
ENV TGT_PLATFORM=$TARGET

USER root

# Install prerequisites
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

## Install JFrog Plugins
RUN fluent-gem install fluent-plugin-concat && \
    fluent-gem install fluent-plugin-splunk-hec && \
    fluent-gem install fluent-plugin-jfrog-siem && \
    fluent-gem install fluent-plugin-jfrog-metrics && \
    fluent-gem install fluent-plugin-jfrog-send-metrics

## Download Config Files
RUN if [ "$SRC_PLATFORM" = "JFRT" ] ; then curl -fsSL https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.rt -o /fluentd/etc/fluent.conf; fi
RUN if [ "$SRC_PLATFORM" = "JFXRAY" ] ; then curl -fsSL https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.xray -o /fluentd/etc/fluent.conf; fi

USER fluent

STOPSIGNAL SIGTERM
