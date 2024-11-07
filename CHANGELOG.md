# JFrog Log Analytics Changelog

All changes to the log analytics integration will be documented in this file.

## [1.0.8] - November 7, 2024

* FluentD sidecar image version bumped to 4.13, to reflect changes in `jfrog_siem` and `jfrog_send_metrics` FluentD plugins 

## [1.0.7] - October 25, 2024

* Add support for metrics and logs outbound payload compression, with `gzip_compression` FluentD param as part of `fluent-plugin-jfrog-sent-metrics` and `fluent-plugin-splunk-hec` plugins
* Add support for a configurable http request timeout, with `request_timeout` FluentD param as part of `fluent-plugin-jfrog-metrics` and `fluent-plugin-jfrog-sent-metrics` plugins
* Add support for a configurable `verify_ssl` FluentD param as part of `fluent-plugin-jfrog-metrics`
* FluentD sidecar version bumped to 4.9, to incorporate the above changes

## [1.0.6] - August 8, 2024

* Fix metrics configuration due to deprication of `artifactory.openMetrics` as part of Artifactory 7.87.x charts and renaming it to `artifactory.metrics`

## [1.0.5] - July 22, 2024

* FluentD sidecar version bumped to 4.5, to upgrade base image to bitnami/fluentd 1.17.0
* Fixing metrics documentation and general readme fixes
* Remove elastic search fluentd plugins from docker images

## [1.0.4] - June6, 2024

* [BREAKING] Adding deprecation notice for partnership-pts-observability.jfrog.io docker registry
* FluentD sidecar version bumped to 4.3, to upgrade base image to bitnami/fluentd 1.16.5
* Update FluentD sidecar helm charts to match recent changes in JFrog's official charts

## [1.0.3] - April 22, 2024

* Fix order of request and response content length to match spec

## [1.0.2] - April 11th, 2024

* Fix Artifactory access's regex to match log input changes

## [1.0.1] - March 22nd, 2024

* Updated docker images to use fluetnd:1.16.3 to resolve existing CVEs. Please see [security section](https://github.com/jfrog/log-analytics-splunk/security) for more info
* Added CI to generate [fluentd side car docker](https://github.com/jfrog/log-analytics-splunk/blob/master/fluentd-installer/Dockerfile.fluentd.sidecar) image from source

## [1.0.0] - May 26th, 2023

* Supporting only OS/VM, Docker and k8s installation types
* Adding .env files instead of setting/filling variables in fluentd config
* Adding jfrog and heap callhome in fluentd config
* Supporting only Artifactory and Xray Fluentd config

## [0.13.0] - Feb 14, 2022

* Added call home implementation to the artifactory fluent configuration

## [0.12.0] - May 26, 2021

### Breaking Changes

* Using unified fluentd configuration for Xray Logs and Violations dashboards
* Using APIKey to authenticate Xray Violations (SIEM fluentd input plugin)
* Sending Xray logs and violation data to the same index
* Using log_source to filter in Xray Violations dashboard queries

## [0.11.2] - Apr 12, 2021

* Fixing violation data correlation with user, ip information

## [0.11.2] - Apr 5, 2021

* Fixing bugs in Dockerhub widgets, Xray Violations Dashboard

## [0.11.1] - Mar 30, 2021

* Renaming widgets, fixing search queries in Xray Violations Dashboard

## [0.11.0] - Mar 3, 2021

* Adding Violations widgets to Xray dashboard

## [0.10.0] - Feb 17, 2021

* Normalizing access, request logs
* Added eventtypes, tags, fields, macros to the app

## [0.9.2] - Feb 3, 2021

* New Widgets to Xray tab to show vulnerability information

## [0.9.1] - Jan 28, 2021

* Helm support for Splunk to deploy Artifactory or Xray via helm with logs sent to Splunk

## [0.9.0] - Dec 1, 2020

* Log Volume charts to show only artifactory and xray logs respectively
* Adding macro to avoid index dependency

## [0.8.0] - Oct 20, 2020

* README updates for new Dockerhub / Docker widgets in Splunkbase app
* Added CHANGELOG-splunkbase.md to mirror release notes in Splunkbase

## [0.7.0] - Oct 20, 2020

* Fixing issue with ip_address in access logs having space and . at the end

## [0.6.0] - Sept 25, 2020

* [BREAKING] Splunk fluentd configs updated to use JF_PRODUCT_DATA_INTERNAL env.

## [0.5.1] - Sept 9, 2020

* Splunk repo now submodule of parent log-analytics.

## [0.5.0] - Sept 8, 2020

* Adding JFrog Pipelines fluent configuration files to capture logs

## [0.4.0] - Sept 4, 2020

* Adding JFrog Mission Control fluent configuration files to capture logs

## [0.3.0] - Aug 26, 2020

* Adding JFrog Distribution fluent configuration files to capture logs

## [0.2.0] - Aug 24, 2020

* Splunk updates to launch new version of Splunkbase app v1.1.0

## [0.1.1] - June 1, 2020

* Removing the need for user to specify splunk host , user, and token twice
* Fixing issue with regex on the audit security log
* Fixed issue with the repo and image when not docker api url

## [0.1.0] - May 12, 2020

* Initial release of Jfrog Logs Analytic integration
