## [1.2.7] - May 26th, 2023
* Verified dashboard widget queries for Artifactory and Xray dashboards
* Updated documentation to setup JFrog Splunk Integration

## [1.2.4] - Jan 17, 2022
* Added Support for Openmetrics Dashboards for Jfrog Artifactory and Jfrog Xray
* To enable and setup Openmentrics follow - https://github.com/jfrog/log-analytics-splunk/tree/Metrics_splunk#readme

## [1.2.3] - Aug 26, 2021
* Updating jQuery version to 3.5.0 or higher

## [1.2.2] - Aug 3, 2021
* Fixing violation data with correlation widget fields

## [1.2.1] - Jun 22, 2021
* App restarts after installation

## [1.2.0] - May 26, 2021
### Breaking Changes
* Using unified fluentd configuration for Xray Logs and Violations dashboards
* Using APIKey to authenticate Xray Violations (SIEM fluentd input plugin)
* Sending Xray logs and violation data to the same index
* Using log_source to filter in Xray Violations dashboard queries

## [1.1.8] - Apr 12th, 2021
* Fixing violation data correlation with user, ip information

## [1.1.7] - Apr 5th, 2021
* Fixing bugs in Dockerhub widgets, Xray Violations Dashboard

## [1.1.6] - Mar 30th, 2021
* Renaming widgets, fixing search queries in Xray Violations Dashboard

## [1.1.5] - Mar 2nd, 2021
* Adding Violations widgets to Xray dashboard

## [1.1.4] - Feb 19th, 2021
* Normalizing access and request logs to Splunk Web and Change CIM
* Added eventtypes, tags, fields, macros to the app

## [1.1.3] - Feb 2nd, 2021
* Added Xray Vulnerability Widgets

## [1.1.2] - Nov 3rd, 2020
* Dockerhub Rate Impacts Javascript Bug Fix

## [1.1.1] - Oct 29, 2020
* Dockerhub Rate Impacts Log Analytic Solution

## [1.1.0] - Aug 25, 2020
• Grouped Artifactory chart widgets under Request, Application and Audit tabs
• Added drilldowns in charts
• Added hostname and sourcetype fields in Splunk events
• Optimized chart SPL queries
• Fixed bugs

## [1.0.1] May 29, 2020
• JFrog Platform Log Analytics v1.0.1 - fixed issue with security audit log widget

## [1.0.0] May 14, 2020
• JFrog Platform Log Analytics v0.1.0