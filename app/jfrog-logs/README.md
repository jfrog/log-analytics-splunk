# JFrog Platform Log Analytics Splunk App

## Getting Started
Install the app in your Splunk instance. Then restart your Splunk instance by going to _Server Controls > Restart_.

## Splunk Setup
1. Create new Events index `jfrog_splunk` (or your custom name) at _Settings > Indexes > New Index > Save_
2. Create new Metrics index `jfrog_splunk_metrics` (or your custom name) at _Settings > Indexes > New Index > Metrics > Save_
3. Create a new HTTP Event Collector data input for logs at _Settings > Data Inputs > HTTP Event Collector > New Token > jfrog_splunk index > Save_
4. Create a new HTTP Event Collector data input for metrics at _Settings > Data Inputs > HTTP Event Collector > New Token > jfrog_splunk_metrics index > Save_

**Note:** You can customize the index names by setting the `SPLUNK_LOGS_INDEX` and `SPLUNK_METRICS_INDEX` environment variables in your configuration.

## Setup Fluentd
FluentD is used to send log events to Splunk. This [repo](https://github.com/jfrog/log-analytics-splunk) contains instructions on various installations options for Fluentd as a logging agent. 

Download the .env file from [here](https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/jfrog.env) and fill in the jfrog.env file with Splunk and JPD information

```
export JF_PRODUCT_DATA_INTERNAL=JF_PRODUCT_DATA_INTERNAL
export SPLUNK_COM_PROTOCOL=http
export SPLUNK_HEC_HOST=splunk.example.com
export SPLUNK_HEC_PORT=8088
export SPLUNK_HEC_TOKEN=SPLUNK_HEC_TOKEN
export SPLUNK_METRICS_HEC_TOKEN=SPLUNK_METRICS_HEC_TOKEN
export SPLUNK_LOGS_INDEX=jfrog_splunk
export SPLUNK_METRICS_INDEX=jfrog_splunk_metrics
export SPLUNK_INSECURE_SSL=false
export SPLUNK_VERIFY_SSL=true
export SPLUNK_COMPRESS_DATA=true
export JPD_URL=http://abc.jfrog.io
export JPD_ADMIN_USERNAME=admin
export JFROG_ADMIN_TOKEN=JFROG_ADMIN_TOKEN
export COMMON_JPD=false
```

* **JF_PRODUCT_DATA_INTERNAL**: The environment variable JF_PRODUCT_DATA_INTERNAL must be defined to the correct location. For each JFrog service you will find its active log files in the `$JFROG_HOME/<product>/var/log` directory
* **SPLUNK_COM_PROTOCOL**: HTTP Scheme, http or https
* **SPLUNK_HEC_HOST**: Splunk Instance URL
* **SPLUNK_HEC_PORT**: Splunk HEC configured port
* **SPLUNK_HEC_TOKEN**: Splunk HEC Token for sending logs to Splunk
* **SPLUNK_METRICS_HEC_TOKEN**: Splunk HEC Token for sending metrics to Splunk
* **SPLUNK_LOGS_INDEX**: Splunk index name for storing logs (default: jfrog_splunk)
* **SPLUNK_METRICS_INDEX**: Splunk index name for storing metrics (default: jfrog_splunk_metrics)
* **SPLUNK_INSECURE_SSL**: false for test environments only or if http scheme
* **SPLUNK_VERIFY_SSL**: false for disabling ssl validation (useful for proxy forwarding or bypassing ssl certificate validation)
* **SPLUNK_COMPRESS_DATA**: true for compressing logs and metrics json payloads on outbound to Splunk
* **JPD_URL**: Artifactory JPD URL of the format `http://<ip_address>`
* **JPD_ADMIN_USERNAME**: Artifactory username for authentication
* **JFROG_ADMIN_TOKEN**: Artifactory [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) for authentication
* **COMMON_JPD**: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

## Dashboards

### Artifactory dashboard
JFrog Artifactory Dashboard is divided into multiple sections Application, Audit, Requests, Docker, System Metrics, Heap Metrics and Connection Metrics

* **Application** - This section tracks Log Volume(information about different log sources) and Artifactory Errors over time(bursts of application errors that may otherwise go undetected)
* **Audit** - This section tracks audit logs help you determine who is accessing your Artifactory instance and from where. These can help you track potentially malicious requests or processes (such as CI jobs) using expired credentials.
* **Requests** - This section tracks HTTP response codes, Top 10 IP addresses for uploads and downloads
* **Docker** - To monitor Dockerhub pull requests users should have a Dockerhub account either paid or free. Free accounts allow up to 200 pull requests per 6 hour window. Various widgets have been added in the new Docker tab under Artifactory to help monitor your Dockerhub pull requests. An alert is also available to enable if desired that will allow you to send emails or add outbound webhooks through configuration to be notified when you exceed the configurable threshold.
* **System Metrics** - This section tracks CPU Usage, System Memory and Disk Usage metrics
* **Heap Metrics** - This section tracks Heap Memory and Garbage Collection
* **Connection Metrics** - This section tracks Database connections and HTTP Connections

### Xray dashboard
JFrog Xray Dashboard is divided into three sections Logs, Violations and Metrics

* **Logs** - This section provides a summary of access, service and traffic log volumes associated with Xray. Additionally, customers are also able to track various HTTP response codes, HTTP 500 errors, and log errors for greater operational insight
* **Violations** - This section provides an aggregated summary of all the license violations and security vulnerabilities found by Xray.  Information is segment by watch policies and rules.  Trending information is provided on the type and severity of violations over time, as well as, insights on most frequently occurring CVEs, top impacted artifacts and components.
* **Metrics** - This section tracks CPU usage, System Memory, Disk Usage, Heap Memory and Database Connections

## CIM Compatibility
Log data from JFrog platform logs is translated to pre-defined Common Information Models (CIM) compatible with Splunk. This compatibility enables new advanced features where users can search and access JFrog log data that is compatible with data models. For example

```text
| datamodel Web Web search
| datamodel Change_Analysis All_Changes search
| datamodel Vulnerabilities Vulnerabilities search
```

## Additional Setup
For complete instructions on setup of the integration between JFrog Artifactory & Xray to Splunk visit our Github [repo](https://github.com/jfrog/log-analytics-splunk)
