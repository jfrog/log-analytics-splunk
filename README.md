# SPLUNK

## Version Matrix
| version | artifactory | xray   | distribution | mission_control | pipelines | splunk                    |
|---------|-------------|--------|--------------|-----------------|-----------|---------------------------|
| 0.9.1   | 7.12.6      | 3.15.3 | 2.6.0        | 4.6.2           | 1.10.0    | 8.0.5 Build: a1a6394cc5ae |
| 0.9.0   | 7.11.5      | 3.15.1 | 2.6.0        | 4.6.2           | 1.10.0    | 8.0.5 Build: a1a6394cc5ae |
| 0.8.0   | 7.10.2      | 3.10.3 | 2.4.2        | 4.5.0           | 1.8.0     | 8.0.5 Build: a1a6394cc5ae |
| 0.7.0   | 7.9.1       | 3.9.1  | 2.4.2        | 4.5.0           | 1.8.0     | 8.0.5 Build: a1a6394cc5ae |
| 0.6.0   | 7.7.8       | 3.8.6  | 2.4.2        | 4.5.0           | 1.7.2     | 8.0.5 Build: a1a6394cc5ae |
| 0.5.0   | 7.7.3       | 3.8.0  | 2.4.2        | 4.5.0           | 1.7.2     | 8.0.5 Build: a1a6394cc5ae |
| 0.4.0   | 7.7.3       | 3.8.0  | 2.4.2        | 4.5.0           | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.3.0   | 7.7.3       | 3.8.0  | 2.4.2        | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.2.0   | 7.7.3       | 3.8.0  | N/A          | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |
| 0.1.1   | 7.6.3       | 3.6.2  | N/A          | N/A             | N/A       | 8.0.5 Build: a1a6394cc5ae |

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

### Configure Splunk

Our integration uses the [Splunk HEC](https://dev.splunk.com/enterprise/docs/dataapps/httpeventcollector/) to send data to Splunk.

Users will need to configure the HEC to accept data (enabled) and also create a new token. Steps are below.

#### Create index jfrog_splunk
````text
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Indexes"
3. Click on "New Index"
4. Enter Index name as jfrog_splunk
5. Click "Save"
````

#### Create index xray_violations
````text 
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Indexes"
3. Click on "New Index"
4. Enter Index name as xray_violations
5. Click "Save"
````   

#### Create Eventtype
````text
1. Open Splunk web console as administrator
2. Click on "Settings" in dropdown select "Event types"
3. Click on "New Event Type"
4. Select destination app as "Search"
5. Enter event type name
6. Enter index="xray_violations" as search string
7. Enter "report, vulnerability" as tags
8. Click "Save" to save your eventtype
9. Click on "Permissions" next to your eventtype
10. Select Object should appear in "This app only"
11. Click "Save"
````

#### Create macro to avoid index dependency
````text
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Advanced Search"
3. Click on "Add New" next to Search macros
4. Under destination app, select "jfrog-logs"
5. Enter name of the macro as "default_index"
6. Under definition, enter "index=jfrog_splunk"
7. Click "Save" to save your macro
8. Click on "Permissions" next to your macro
9. Select Object should appear in "This app only"
10. Click "Save"
````

#### Configure new HEC token to receive Logs
````text
1. Open Splunk web console as adminstrator
2. Click on "Settings" in dropdown select "Data inputs"
3. Click on "HTTP Event Collector"
4. Click on "New Token"
5. Enter a "Name" in the textbox
6. (Optional) Enter a "Description" in the textbox
7. Click on the green "Next" button
8. Select App Context of "JFrog Platform Log Analytics" in the dropdown
9. Add "jfrog_splunk" index to store the JFrog platform log data into.
10. Click on the green "Review" button
11. If good, Click on the green "Done" button
12. Save the generated token value
````

#### Configure new HEC token to receive Xray Violations
````text 
1. Open Splunk web cobsole as administrator
2. Click on "Settings" in dropdown select "Data inputs"
3. Click on "HTTP Event Collector"
4. Click on "New Token"
5. Enter a "Name" in the textbox
6. (Optional) Enter a "Description" in the textbox
7. Click on the green "Next" button
8. Select sourcetype by Clicking on Select > Select Source Type dropdown > Structured > _json
9. Add "xray_violations" index to store the JFrog platform log data into.
10. Click on the green "Review" button
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
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/splunk/splunk_siem.conf
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

#### Configure fluentd configuration files

Users need to specify the following match directive parameters in fluent.conf and splunk_siem.conf files

````text
HEC_HOST   -> IP address or DNS of Splunk HEC 
HEC_PORT   -> Splunk HEC port default is 8088
HEC_TOKEN  -> Saved generated token above
````

These values override the last section of the `fluentd.conf` and `splunk_siem.conf` shown below:
``` 
<match jfrog.**>
  @type splunk_hec
  host HEC_HOST
  port HEC_PORT
  token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

If ssl is enabled the ca_file will be used and must be supplied.

index in match directive is `jfrog_splunk` for fluent configurations and `xray_violations` for siem configuration

#### Configure splunk_siem.conf

Integration is done by setting up Xray. Obtain JPD url and access token for API. Configure the source directive parameters specified below

* **tag** (string) (required): The value is the tag assigned to the generated events.
* **jpd_url** (string) (required): JPD url required to pull Xray SIEM violations
* **access_token** (string) (required): [Access token](https://www.jfrog.com/confluence/display/JFROG/Access+Tokens) to authenticate Xray
* **pos_file** (string) (required): Position file to record last SIEM violation pulled
* **batch_size** (integer) (optional): Batch size for processing violations
    * Default value: `25`
* **thread_count** (integer) (optional): Number of workers to process violation records in thread pool
    * Default value: `5`
* **wait_interval** (integer) (optional): Wait interval between pulling new events
    * Default value: `60`

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


## Fluentd Install

### OS / Virtual Machine

Recommended install is through fluentd's native OS based package installs:

| OS            | Package Manager | Link |
|---------------|-----------------|------|
| CentOS/RHEL   | RPM (YUM)       | https://docs.fluentd.org/installation/install-by-rpm |
| Debian/Ubuntu | APT             | https://docs.fluentd.org/installation/install-by-deb |
| MacOS/Darwin  | DMG             | https://docs.fluentd.org/installation/install-by-dmg |
| Windows       | MSI             | https://docs.fluentd.org/installation/install-by-msi |

User installs can utilize the zip installer for Linux

| OS            | Package Manager | Link |
|---------------|-----------------|------|
| Linux (x86_64)| ZIP             | https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz |

Download it to a directory the user has permissions to write such as the `$JF_PRODUCT_DATA_INTERNAL` locations discussed above:

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz
````

Untar to create the folder:
````text
tar -xvf fluentd-1.11.0-linux-x86_64.tar.gz
````
Move into the new folder:

````text
cd fluentd-1.11.0-linux-x86_64
````
Run the fluentd wrapper with one argument pointed to the configuration file to load:

````text
./fluentd test.conf
````

Next steps are to setup a  `fluentd.conf` file using the relevant integrations for Splunk, DataDog, Elastic, or Prometheus.

### Docker

Recommended install for Docker is to utilize the zip installer for Linux

| OS            | Package Manager | Link |
|---------------|-----------------|------|
| Linux (x86_64)| ZIP             | https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz |

Download it to a directory the user has permissions to write such as the `$JF_PRODUCT_DATA_INTERNAL` locations discussed above:

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz
````

Untar to create the folder:
````text
tar -xvf fluentd-1.11.0-linux-x86_64.tar.gz
````
Move into the new folder:

````text
cd fluentd-1.11.0-linux-x86_64
````
Run the fluentd wrapper with one argument pointed to the configuration file to load:

````text
./fluentd test.conf
````

Next steps are to setup a  `fluentd.conf` file using the relevant configuration files for Splunk.

### Kubernetes

Recommended install for Kubernetes is to utilize the helm chart with the associated values.yaml in this repo.

| Product | Example Values File |
|---------|-------------|
| Artifactory | helm/artifactory-values.yaml |
| Artifactory HA | helm/artifactory-ha-values.yaml |
| Xray | helm/xray-values.yaml |

Update the values.yaml associated to the product you want to deploy with your Splunk settings.

Then deploy the helm chart such as below:

Artifactory ⎈:
```text
helm upgrade --install artifactory-ha  jfrog/artifactory-ha \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/artifactory-values.yaml
```

Artifactory-HA ⎈:
```text
helm upgrade --install artifactory-ha  jfrog/artifactory-ha \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/artifactory-ha-values.yaml
```

Xray ⎈:
```text
helm upgrade --install xray jfrog/xray --set xray.jfrogUrl=http://my-artifactory-nginx-url \
       --set xray.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set xray.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/xray-values.yaml
```

#### Kubernetes Deployment without Helm

To modify existing Kubernetes based deployments without using Helm users can use the zip installer for Linux:

| OS            | Package Manager | Link |
|---------------|-----------------|------|
| Linux (x86_64)| ZIP             | https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz |

Download it to a directory the user has permissions to write such as the `$JF_PRODUCT_DATA_INTERNAL` locations discussed above:

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://github.com/jfrog/log-analytics/raw/master/fluentd-installer/fluentd-1.11.0-linux-x86_64.tar.gz
````

Untar to create the folder:
````text
tar -xvf fluentd-1.11.0-linux-x86_64.tar.gz
````
Move into the new folder:

````text
cd fluentd-1.11.0-linux-x86_64
````
Run the fluentd wrapper with one argument pointed to the configuration file to load:

````text
./fluentd test.conf
````

Next steps are to setup a  `fluentd.conf` file using the relevant configuration files for Splunk.


## Dockerhub Pull Requests

To monitor Dockerhub pull requests users should have a Dockerhub account either paid or free.

Free accounts allow up to 200 pull requests per 6 hour window.

Various widgets have been added in the new Docker tab under Artifactory to help monitor your Dockerhub pull requests.

An alert is also available to enable if desired that will allow you to send emails or add outbound webhooks through configuration to be notified when you exceed the configurable threshold.

## Xray Violations

With Xray integration, new widgets have been added to provide insights into the type, frequency and volume of violations that are reported as a result of Xray scanning.  

The vulnerability count widget provides a historical summary of all the high, medium and low level vulnerabilities reported over a selectable time period.  For example, customers can quickly view the number of critical (high level) vulnerabilities that have been reported by Xray over the time.  

The top 10 CVE table provides information about the most frequently occurring vulnerabilities over a period of time. 

The vulnerability table provides additional information on each of the violations including the time of occurence, type of violation, impacted artifacts and other details that allow customers to trace the impact of the violation. 


## Splunk Demo

To run this integration for Splunk users can create a Splunk instance with the correct ports open in Kubernetes by applying the yaml file:

``` 
kubectl apply -f k8s/splunk.yaml
```

This will create a new Splunk instance you can use for a demo to send your JFrog logs over to.

Once they have a Splunk up for demo purposes they will need to configure the HEC and then update fluent config files with the relevant parameters for HEC_HOST, HEC_PORT, & HEC_TOKEN.

They can now access Splunk to view the JFrog dashboard as new data comes in.

## References

* [Fluentd](https://www.fluentd.org) - Fluentd Logging Aggregator/Agent
* [Splunk](https://www.splunk.com/) - Splunk Logging Platform
* [Splunk HEC](https://dev.splunk.com/enterprise/docs/dataapps/httpeventcollector/) - Splunk HEC used to upload data into Splunk
