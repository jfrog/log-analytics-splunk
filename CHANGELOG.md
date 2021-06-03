# JFrog Log Analytics Changelog
All changes to the log analytics integration will be documented in this file.

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

