# SPLUNK

## Version Matrix
| version | artifactory | xray   | distribution | mission_control | pipelines | splunk                    |
|---------|-------------|--------|--------------|-----------------|-----------|---------------------------|
| 0.8.0   | 7.10.2      | 3.10.3 | 2.4.2        | 4.5.0           | 1.8.0     | 8.0.5 Build: a1a6394cc5ae |
| 0.7.0   | 7.9.1       | 3.9.1  | 2.4.2        | 4.5.0           | 1.8.0     | 8.0.5 Build: a1a6394cc5ae |
| 0.6.0   | 7.7.8       | 3.8.6  | 2.4.2        | 4.5.0           | 1.7.2     | 8.0.5 Build: a1a6394cc5ae |
| 0.5.0   | 7.7.3       | 3.8.0  | 2.4.2        | 4.5.0           | 1.7.2     | 8.0.5 Build: a1a6394cc5ae |
| 0.4.0   | 7.7.3       | 3.8.0  | 2.4.2        | 4.5.0           | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.3.0   | 7.7.3       | 3.8.0  | 2.4.2        | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.2.0   | 7.7.3       | 3.8.0  | N/A          | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.1.1   | 7.6.3       | 3.6.2  | N/A          | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |

## Required Environment Variable

`JF_PRODUCT_DATA_INTERNAL` is required check if already set if not see example values below:

Artifactory: 
````text
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/artifactory/
````

Xray:
````text
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/xray/
````

Mision Control:
````text
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/mc/
````

Distribution:
````text
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/distribution/
````

Pipelines:
````text
export JF_PRODUCT_DATA_INTERNAL=/opt/jfrog/pipelines/var/
````

## Log Collector Requirement

Fluentd is the supported log collector for this integration.

Fluentd setup must be completed prior to Splunk.

For Fluentd setup information read the JFrog log analytic repository's [README.](https://github.com/jfrog/log-analytics/blob/master/README.md)

## Splunk Configuration

`Note! You must follow the order of the steps throughout Splunk Configuration`

### Splunkbase App

Install the `JFrog Log Analytics Platform` app from Splunkbase [here!](https://splunkbase.splunk.com/app/5023/)

````text
1. Download file from Splunkbase
2. Open Splunk web console as adminstrator
3. From homepage click on settings wheel in top right of Apps section
4. Click on "Install app from file"
5. Select download file from Splunkbase on your computer
6. Click upgrade 
7. Click upload
````


Restart Splunk post installation of App.

````text 
1. Open Splunk web console as adminstrator
2. Click on Settings then Server Controls
3. Click on Restart 
````

Login to Splunk after the restart completes.

Confirm the version is the latest version available in Splunkbase.

### Configure Splunk HEC

Our integration uses the [Splunk HEC](https://dev.splunk.com/enterprise/docs/dataapps/httpeventcollector/) to send data to Splunk.

Users will need to configure the HEC to accept data (enabled) and also create a new token. Steps are below.

#### Configure HEC to accept data
````text
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Data inputs"
3. Click on "HTTP Event Collector"
4. Click on "Global Settings"
5. If Disabled, Click on All Tokens "Enabled"
6. (Optional) HTTPS check SSL enabled or HTTP uncheck SSL enabled flag
7. Click "Save"
````

#### Configure new HEC token
````text
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Data inputs"
3. Click on "HTTP Event Collector"
4. Click on "New Token"
5. Enter a "Name" in the textbox
6. (Optional) Enter a "Description" in the textbox
7. Click on the green "Next" button
8. Select App Context of "JFrog Platform Log Analytics" in the dropdown
9. Add the index to store the JFrog platform log data into.
10. CLick on the green "Review" button
11. If good, Click on the green "Done" button
12. Save the generated token value
````

### Configure Fluentd Splunk Output

#### Download fluentd.conf
Artifactory: 
````text
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.rt
````

Xray:
````text
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.xray
````

Mision Control:
````text
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.missioncontrol
````

Distribution:
````text
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.distribution
````

Pipelines:
````text
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.pipelines
````


#### Configure fluentd.conf

Users need to specify the following parameters:

````text
HEC_HOST   -> IP address or DNS of Splunk HEC 
HEC_PORT   -> Splunk HEC port default is 8088
HEC_TOKEN  -> Saved generated token above
````

These values override the last section of the `fluentd.conf` shown below:
``` 
<match jfrog.**>
  @type splunk_hec
  host HEC_HOST
  port HEC_PORT
  token HEC_TOKEN
  format json
  # buffered output parameter
  flush_interval 10s
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

If ssl is enabled the ca_file will be used and must be supplied.


## Dockerhub Pull Requests

To monitor Dockerhub pull requests users should have a Dockerhub account either paid or free.

Free accounts allow up to 200 pull requests per 6 hour window.

Various widgets have been added in the new Docker tab under Artifactory to help monitor your Dockerhub pull requests.

An alert is also available to enable if desired that will allow you to send emails or add outbound webhooks through configuration to be notified when you exceed the configurable threshold.

## Splunk Demo

To run this integration for Splunk users can create a Splunk instance with the correct ports open in Kubernetes by applying the yaml file:

``` 
kubectl apply -f splunk.yaml
```

This will create a new Splunk instance you can use for a demo to send your JFrog logs over to.

Once they have a Splunk up for demo purposes they will need to configure the HEC and then update fluent config files with the relevant parameters for HEC_HOST, HEC_PORT, & HEC_TOKEN.

They can now access Splunk to view the JFrog dashboard as new data comes in.

## References

* [Fluentd](https://www.fluentd.org) - Fluentd Logging Aggregator/Agent
* [Splunk](https://www.splunk.com/) - Splunk Logging Platform
* [Splunk HEC](https://dev.splunk.com/enterprise/docs/dataapps/httpeventcollector/) - Splunk HEC used to upload data into Splunk
