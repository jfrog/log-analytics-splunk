# Dockerfile for fluentd sidecar image with all the necessary plugins for our log analytic providers
FROM releases-docker.jfrog.io/fluentd:4.22
LABEL maintainer="Partner Engineering <partner_support@jfrog.com>"

## Build time Arguments, short circuit them to ENV Variables so they are available at run time also
ARG SOURCE=JFRT
ARG TARGET=SPLUNK

## Environment Variables set by this docker file, there will be seperate env params set by a env file while running the containers
## For better maintainability always depend dockerfile code on the environment variables declared in this file to add more platforms
ENV SRC_PLATFORM=$SOURCE
ENV TGT_PLATFORM=$TARGET

USER root

## Download Config Files
## The released image releases-docker.jfrog.io/fluentd:4.22 already ships fluentd 1.19.3, curl, and every
## plugin this integration needs (fluent-plugin-splunk-hec, -jfrog-siem, -jfrog-metrics, -jfrog-send-metrics,
## -concat, ...), so no fluent-gem install or apt-get step is required here.
RUN if [ "$SRC_PLATFORM" = "JFRT" ] ; then curl -fsSL https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.rt -o /fluentd/etc/fluent.conf; fi
RUN if [ "$SRC_PLATFORM" = "JFXRAY" ] ; then curl -fsSL https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.xray -o /fluentd/etc/fluent.conf; fi

USER fluent

STOPSIGNAL SIGTERM
