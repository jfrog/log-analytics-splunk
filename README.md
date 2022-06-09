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

## Table of Contents
`Note! You must follow the order of the steps throughout Splunk Configuration`

1. [Splunk Setup](#splunk-setup)
2. [Environment Configuration](#environment-configuration)
3. [Fluentd Installation](#fluentd-installation)
    * [OS / Virtual Machine](#os--virtual-machine)
    * [Docker](#docker)
    * [Kubernetes Deployment with Helm](#kubernetes-deployment-with-helm)
4. [Fluentd Configuration for Splunk](#fluentd-configuration-for-splunk)
    * [Configuration steps for Artifactory](#configuration-steps-for-artifactory)
    * [Configuration steps for Xray](#configuration-steps-for-xray)
    * [Configuration steps for Mission Control](#configuration-steps-for-mission-control)
    * [Configuration steps for Distribution](#configuration-steps-for-distribution)
    * [Configuration steps for Pipelines](#configuration-steps-for-pipelines)
5. [Dashboards](#dashboards)
6. [Splunk Demo](#splunk-demo)
7. [References](#references)

## Splunk Setup

### Splunkbase App

Install the `JFrog Log Analytics Platform` app from Splunkbase [here!](https://splunkbase.splunk.com/app/5023/)

````text
1. Download file from Splunkbase
2. Open Splunk web console as administrator
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
1. Open Splunk web console as administrator
2. Click on "Settings" in dropdown select "Indexes"
3. Click on "New Index"
4. Enter Index name as jfrog_splunk
5. Click "Save"
````

#### Create index jfrog_splunk_metrics
````text
1. Open Splunk web console as administrator
2. Click on "Settings" in dropdown select "Indexes"
3. Click on "New Index"
4. Enter Index name as jfrog_splunk_metrics
5. Click on 'Metrics' for the 'Index Data Type' 
6. Click "Save"
````

#### Configure new HEC token to receive Logs
````text
1. Open Splunk web console as administrator
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

## Environment Configuration

We rely heavily on environment variables so that the correct log files are streamed to your observability dashboards. Ensure that you set the JF_PRODUCT_DATA_INTERNAL environment variable to the correct path for your product

The environment variable JF_PRODUCT_DATA_INTERNAL must be defined to the correct location.

Helm based installs will already have this defined based upon the underlying docker images.

For non-k8s based installations below is a reference to the Docker image locations per product. Note these locations may be different based upon the installation location chosen.

````text
Artifactory: 
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/artifactory/
````

````text
Xray:
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/xray/
````

````text
Nginx:
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/nginx/
````

````text
Mission Control:
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/mc/
````

````text
Distribution:
export JF_PRODUCT_DATA_INTERNAL=/var/opt/jfrog/distribution/
````

````text
Pipelines:
export JF_PRODUCT_DATA_INTERNAL=/opt/jfrog/pipelines/var/
````

## Fluentd Installation

### OS / Virtual Machine

Ensure you have access to the Internet from VM. Recommended install is through fluentd's native OS based package installs:

| OS             | Package Manager        | Link                                                 |
|----------------|------------------------|------------------------------------------------------|
| CentOS/RHEL    | Linux - RPM (YUM)      | https://docs.fluentd.org/installation/install-by-rpm |
| Debian/Ubuntu  | Linux - APT            | https://docs.fluentd.org/installation/install-by-deb |
| MacOS/Darwin   | MacOS - DMG            | https://docs.fluentd.org/installation/install-by-dmg |
| Windows        | Windows - MSI          | https://docs.fluentd.org/installation/install-by-msi |
| Gem Install**	 | MacOS & Linux - Gem			 | https://docs.fluentd.org/installation/install-by-gem | 


```text
** For Gem based install, Ruby Interpreter has to be setup first, following is the recommended process to install Ruby

1. Install Ruby Version Manager (RVM) as described in https://rvm.io/rvm/install#installation-explained, ensure to follow all the onscreen instructions provided to complete the rvm installation
	* For installation across users a SUDO based install is recommended, the installation is as described in https://rvm.io/support/troubleshooting#sudo

2. Once rvm installation is complete, verify the RVM installation executing the command 'rvm -v'

3. Now install ruby v2.7.0 or above executing the command 'rvm install <ver_num>', ex: 'rvm install 2.7.5'

4. Verify the ruby installation, execute 'ruby -v', gem installation 'gem -v' and 'bundler -v' to ensure all the components are intact

5. Post completion of Ruby, Gems installation, the environment is ready to further install new gems, execute the following gem install commands one after other to setup the needed ecosystem

	'gem install fluentd'

```

After FluentD is successfully installed, the below plugins are required to be installed

```text

	'gem install fluent-plugin-splunk-hec'
	'gem install fluent-plugin-jfrog-siem'
	'gem install fluent-plugin-jfrog-metrics'

```


Configure `fluent.conf.*` according to the instructions mentioned in [Fluentd Configuration for Splunk](#fluentd-configuration-for-splunk) section and then run the fluentd wrapper with one argument pointed to the `fluent.conf.*` file configured.

````text
./fluentd $JF_PRODUCT_DATA_INTERNAL/fluent.conf.<product_name>
````


### Docker

In order to run fluentd as a docker image to send the log, siem and metrics data to splunk, the following commands needs to be executed on the host that runs the docker.

1. Check the docker installation is functional, execute command 'docker version' and 'docker ps'.

2. Once the version and process are listed successfully, build the intended docker image for the observability platform using the docker file,

	* Download Dockerfile from [here](https://raw.githubusercontent.com/jfrog/log-analytics/master/docker-build/Dockerfile) to any directory which has write permissions.

3. Download the Dockerenvfile_<observability_platform>.txt file needed to run Jfrog/FluentD Docker Images for the intended observability platform,

	* Download Dockerenvfile_splunk.txt from [here](https://raw.githubusercontent.com/jfrog/log-analytics/master/docker-build/Dockerenvfile_splunk.txt) to the directory where the docker file was downloaded.

```text

For Splunk as the observability platform, execute these commands to setup the docker container running the fluentd installation

1. Execute 'docker build --build-arg SOURCE="JFRT" --build-arg TARGET="SPLUNK" -t <image_name> .'

Command example

'docker build --build-arg SOURCE="JFRT" --build-arg TARGET="SPLUNK" -t jfrog/fluentd-splunk-rt .'

The above command will build the docker image.

2. Fill the necessary information in the Dockerenvfile_splunk.txt file, if the value for any of the field requires to have a '/' use '\/' and if '\' is required use '\\'.

3. Execute 'docker run -it --name jfrog-fluentd-splunk-rt -v <path_to_logs>:/var/opt/jfrog/artifactory --env-file Dockerenvfile_splunk.txt <image_name>' 

The <path_to_logs> should be an absolute path where the Jfrog Artifactory Logs folder resides, i.e for an Docker based Artifactory Installation,  ex: /var/opt/jfrog/artifactory/var/logs on the docker host.

Command example

'docker run -it --name jfrog-fluentd-splunk-rt -v /var/opt/jfrog/artifactory/var:/var/opt/jfrog/artifactory --env-file Dockerenvfile_splunk.txt jfrog/fluentd-splunk-rt'


```

### Kubernetes Deployment with Helm

Recommended installation for Kubernetes is to utilize the helm chart with the associated values.yaml in this repo.

| Product        | Example Values File             |
|----------------|---------------------------------|
| Artifactory    | helm/artifactory-values.yaml    |
| Artifactory HA | helm/artifactory-ha-values.yaml |
| Xray           | helm/xray-values.yaml           |

Update the values.yaml associated to the product you want to deploy with your Splunk settings.

Then deploy the helm chart as described below:

Add JFrog Helm repository:

```text
helm repo add jfrog https://charts.jfrog.io
helm repo update
```

Replace placeholders with your ``masterKey`` and ``joinKey``. To generate each of them, use the command
``openssl rand -hex 32``


JFrog Platform ⎈:

```textmate
!!!Important Note!!!: Platform Chart Deployment shown here is for reference purpose only, 
Kindly apply these charts only after reviewing the options and make necessary changes that suits your deployment
```

Pre-requisite for Observability integration with JFrog Platform charts 

First, install the JFrog Platform chart by configuring intended replicaCount for Artifactory, Xray and enable or disable the solutions

Refer the sample yaml for reference, download [here](https://github.com/jfrog/log-analytics-splunk/blob/master/helm/jfrog-platform-values-without-splunk.yaml)

```yaml

installerInfo: '{ "productId": "Helm_splunk_artifactory/{{ .Chart.Version }}", "features": [ { "featureId": "ArtifactoryVersion/{{ default .Chart.AppVersion .Values.artifactory.image.version }}" }, { "featureId": "{{ if .Values.postgresql.enabled }}postgresql{{ else }}{{ .Values.database.type }}{{ end }}/0.0.0" }, { "featureId": "Platform/{{ default "kubernetes" .Values.installer.platform }}" },  { "featureId": "Channel/Helm_splunk_artifactory" } ] }'
artifactory:
  artifactory:
    openMetrics:
      enabled: true
xray:
  enabled: true
  replicaCount: 1  
insight:
  enabled: false
distribution:
  enabled: false
pipelines:
  enabled: false
rabbitmq:
  enabled: true
redis:
  enabled: false

```
To install the JFrog Platform with the above said configurations run the following command, (note the namespaces and adjust as needed by your deployment requirement)

```kubernetes helm
helm upgrade --install jfrog-platform --namespace jfrog-platform jfrog/jfrog-platform -f jfrog-platform-values-without-splunk.yaml
```

Once the platform is accessible, login to the platform and perform the setup as directed on the UI.

Second, when your JFrog Platform is ready and accessible, the following should be noted

1. Access Token - click [here](https://www.jfrog.com/confluence/display/JFROG/Access+Tokens#AccessTokens-GeneratingScopedTokens) to know how to generate a admin scoped access token
2. API Key - click [here](https://www.jfrog.com/confluence/display/RTF6X/Updating+Your+Profile#UpdatingYourProfile-APIKey) to generate an API Key with profile update process
3. User - admin
Refer [Fluentd Configuration for Splunk](#fluentd-configuration-for-splunk) section for configuring the below parameters
3. Splunk Host - Splunk Instance URL
4. Splunk Token - Token for sending data to Splunk
5. Splunk Port - Splunk HEC Collector configured port
6. COM Protocol - HTTP Scheme, http or https
7. Insecure SSL - false for test environments only or if http scheme

Once the values are noted, download the file to apply the JFrog Platform Upgrade for Splunk from [here](https://github.com/jfrog/log-analytics-splunk/blob/master/helm/jfrog-platform-values.yaml)

Replace the respective values for the following in the global segment of chart values, review the chart values and apply them accordingly

```yaml
global:
  splunk:
    host: splunk.example.com
    port: 8088
    token: splunk_hec_token
    metrics_token: splunk_metrics_hec_token
    index: jfrog_splunk
    com_protocol: https
    insecure_ssl: false
  jfrog:
    observability:
      metrics:
        jpd_url: http://localhost:8082
        jpd_url_nginx: http://jfrog-platform-artifactory-nginx
        username: jfrog_user
        apikey: jfrog_api_key
        token: jfrog_token
      branch: master
```

Once the values are replaced, apply the upgrade as mentioned

1. Get the JFrog Platform Postgres Password and store it to a variable
```shell
export POSTGRES_PASSWORD=kubectl get secret -n jfrog-platform  jfrog-platform-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode
```
2. Using the password obtained, run the following upgrade command
```kubernetes helm
helm upgrade jfrog-platform --namespace jfrog-platform jfrog/jfrog-platform --set databaseUpgradeReady=true --set postgresql.postgresqlPassword=$POSTGRES_PASSWORD -f jfrog-platform-values.yaml
```

```textmate
!!!Important Note!!!: Platform Chart Deployment shown here is for reference purpose only, 
Kindly apply these charts only after reviewing the options and make necessary changes that suits your deployment
```

Artifactory ⎈:

Replace the `splunk.example.com` in `splunk.host` at the end of the yaml file with IP address or DNS of Splunk HEC

Replace the `splunk_hec_token` in `splunk.token` at the end of the yaml file with the HEC Token created in [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs) section and then run the following helm command:

```text
helm upgrade --install artifactory  jfrog/artifactory \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/artifactory-values.yaml
```

Artifactory-HA ⎈:

For HA installation, please create a license secret on your cluster prior to installation.

```text
kubectl create secret generic artifactory-license --from-file=<path_to_license_file>artifactory.cluster.license 
```

Note: Replace placeholders with your ``masterKey`` and ``joinKey``. To generate each of them, use the command
``openssl rand -hex 32``

Replace the `splunk.example.com` in `splunk.host` at the end of the yaml file with splunk IP address or DNS of Splunk HEC

Replace the `splunk_hec_token` in `splunk.token` at the end of the yaml file with the HEC Token created in [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs) section and then run the following helm command:

```text
helm upgrade --install artifactory-ha  jfrog/artifactory-ha \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/artifactory-ha-values.yaml
```

Xray ⎈:

Update the following fields in `/helm/xray-values.yaml`:

Replace the `splunk.example.com` in `splunk.host` at the end of the yaml file with splunk IP address or DNS of Splunk HEC

Replace the `splunk_hec_token` in `splunk.token` at the end of the yaml file with the HEC Token created in [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs) section

Replace `jfrog_user` in `jfrog.siem.username` with Artifactory username 

Replace `jfrog_api_key` in `jfrog.siem.apikey` with [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) and then run the following helm command:

Use the same `joinKey` as you used in Artifactory installation to allow Xray node to successfully connect to Artifactory.

```text
helm upgrade --install xray jfrog/xray --set xray.jfrogUrl=http://my-artifactory-nginx-url \
       --set xray.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set xray.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       -f helm/xray-values.yaml
```

## Fluentd Configuration for Splunk

Download and configure the relevant fluentd.conf files for Splunk

### Configuration steps for Artifactory

Download the artifactory fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.rt
````

For log analytics data to be sent to splunk, Override the match directive(jfrog.**) of the downloaded `fluent.conf.rt` with the details given below

```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

For open metrics data to be sent to splunk, override the match directive(jfrog.metrics.**) of the downloaded `fluent.conf.rt` with the details given below

```
<match jfrog.metrics.**>
  @type splunk_hec
  datatype metric
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token METRICS_HEC_TOKEN
  index jfrog_splunk_metrics
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

```

<source>
  @type jfrog_metrics
  @id metrics_http_jfrt
  tag jfrog.metrics.artifactory
  interval 5s
  metric_prefix 'jfrog.artifactory'
  jpd_url JPD_URL
  username USERNAME
  apikey API_KEY
  token JFROG_ADMIN_TOKEN
  common_jpd false
</source>
```
_**required**_: ```JPD_URL``` is the Artifactory JPD URL of the format `http://<ip_address>`

_**required**_: ```USERNAME``` is the Artifactory username for authentication

_**required**_: ```API_KEY``` is the [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) for authentication

_**required**_: ```JFROG_ADMIN_TOKEN``` is the [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) for authentication

## Note

* For Artifactory v7.4 and below only API Key must be used,
* For Artifactory v7.4 to 7.29 either Token or API Key can be used,
* For Artifactory v7.30 and above token only must be used. 

* common_jpd: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray

	* ex: https://sample_base_url/artifactory or https://sample_base_url/xray 

### Configuration steps for Xray

Download the Xray fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.xray
````

Fill in the JPD_URL, USER, JFROG_API_KEY fields in the source directive of the downloaded `fluent.conf.xray` with the details given below

```text
<source>
  @type jfrog_siem
  tag jfrog.xray.siem.vulnerabilities
  jpd_url JPD_URL
  username USER
  apikey JFROG_API_KEY
  pos_file_path "#{ENV['JF_PRODUCT_DATA_INTERNAL']}/log/jfrog_siem.log.pos"
  from_date "2016-01-01"
</source>
```

_**required**_: ```JPD_URL``` is the Artifactory JPD URL of the format `http://<ip_address>` with is used to pull Xray Violations

_**required**_: ```USER``` is the Artifactory username for authentication

_**required**_: ```JFROG_API_KEY``` is the [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) for authentication

_**optional**_: If not specified, value is set to current date. Setting from_date value will result in violations from the specified date


For open metrics data to be sent to splunk, Fill in the JPD_URL, USER, JFROG_API_KEY fields in the source directive of the downloaded `fluent.conf.xray` with the details given below for the source

```text
<source>
  @type jfrog_metrics
  @id metrics_http_jfrt
  tag jfrog.metrics.xray
  interval 5s
  metric_prefix 'jfrog.xray'
  jpd_url JPD_URL
  username USERNAME
  apikey JFROG_API_KEY
  token JFROG_ADMIN_TOKEN
  common_jpd false
</source>
```

_**required**_: ```JPD_URL``` is the Artifactory JPD URL of the format `http://<ip_address>` with is used to pull Xray Violations

_**required**_: ```USER``` is the Artifactory username for authentication

_**required**_: ```JFROG_API_KEY``` is the [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) for authentication

_**required**_: ```JFROG_ADMIN_TOKEN``` is the [Artifactory API Key](https://www.jfrog.com/confluence/display/JFROG/User+Profile#UserProfile-APIKey) for authentication

## Note

* For Artifactory v7.4 and below only API Key must be used,
* For Artifactory v7.4 to 7.29 either Token or API Key can be used,
* For Artifactory v7.30 and above token only must be used. 

* common_jpd: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray

	* ex: https://sample_base_url/artifactory or https://sample_base_url/xray 


```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

For open metrics data to be sent to splunk, override the match directive(jfrog.metrics.**) of the downloaded `fluent.conf.xray` with the details given below

```
<match jfrog.metrics.**>
  @type splunk_hec
  datatype metric
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token METRICS_HEC_TOKEN
  index jfrog_splunk_metrics
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

```

### Configuration steps for Nginx

Download the Nginx fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.nginx
````

Override the match directive(last section) of the downloaded `fluent.conf.nginx` with the details given below

```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

### Configuration steps for Mission Control

Download the Mission Control fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.missioncontrol
````

Override the match directive(last section) of the downloaded `fluent.conf.missioncontrol` with the details given below

```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

### Configuration steps for Distribution

Download the distribution fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.distribution
````

Override the match directive(last section) of the downloaded `fluent.conf.distribution` with the details given below

```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

### Configuration steps for Pipelines

Download the pipelines fluentd configuration file to a directory the user has permissions to write, such as the $JF_PRODUCT_DATA_INTERNAL locations discussed above in the [Environment Configuration](#environment-configuration) section.

````text
cd $JF_PRODUCT_DATA_INTERNAL
wget https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/fluent.conf.pipelines
````

Override the match directive(last section) of the downloaded `fluent.conf.pipelines` with the details given below

```
<match jfrog.**>
  @type splunk_hec
  protocol COM_PROTOCOL
  hec_host HEC_HOST
  hec_port HEC_PORT
  hec_token HEC_TOKEN
  index jfrog_splunk
  format json
  # buffered output parameter
  flush_interval 10s
  insecure_ssl INSECURE_SSL
  # ssl parameter
  use_ssl false
  ca_file /path/to/ca.pem
</match>
```

_**required**_: ```COM_PROTOCOL``` will be either 'http' or 'https' based on Splunk Server URL

_**required**_: ```HEC_HOST``` is the IP address or DNS of Splunk HEC

_**required**_: ```HEC_PORT``` is the Splunk HEC port which by default is 8088

_**required**_: ```HEC_TOKEN``` is the saved generated token from [Configure new HEC token to receive Logs](#configure-new-hec-token-to-receive-logs)

_**required**_: ```INSECURE_SSL```if set to 'false' Splunk Host Server SSL Certificate is required, fill the ca_file path, if ssl is enabled, ca file will be used and must be supplied,
else set this to 'true' to bypass SSL certificate verification.

## Dashboards

### Artifactory dashboard
JFrog Artifactory Dashboard is divided into three sections Application, Audit, Requests and Docker

* **Application** - This section tracks Log Volume(information about different log sources) and Artifactory Errors over time(bursts of application errors that may otherwise go undetected)
* **Audit** - This section tracks audit logs help you determine who is accessing your Artifactory instance and from where. These can help you track potentially malicious requests or processes (such as CI jobs) using expired credentials.
* **Requests** - This section tracks HTTP response codes, Top 10 IP addresses for uploads and downloads
* **Docker** - To monitor Dockerhub pull requests users should have a Dockerhub account either paid or free. Free accounts allow up to 200 pull requests per 6 hour window. Various widgets have been added in the new Docker tab under Artifactory to help monitor your Dockerhub pull requests. An alert is also available to enable if desired that will allow you to send emails or add outbound webhooks through configuration to be notified when you exceed the configurable threshold.

### Xray dashboard
JFrog Xray Dashboard is divided into two sections Logs and Violations

* **Logs** - This dashboard provides a summary of access, service and traffic log volumes associated with Xray. Additionally, customers are also able to track various HTTP response codes, HTTP 500 errors, and log errors for greater operational insight
* **Violations** - This dashboard provides an aggregated summary of all the license violations and security vulnerabilities found by Xray.  Information is segment by watch policies and rules.  Trending information is provided on the type and severity of violations over time, as well as, insights on most frequently occurring CVEs, top impacted artifacts and components.  

### CIM Compatibility

Log data from JFrog platform logs is translated to pre-defined Common Information Models (CIM) compatible with Splunk. This compatibility enables new advanced features where users can search and access JFrog log data that is compatible with data models. For example

```text
| datamodel Web Web search
| datamodel Change_Analysis All_Changes search
| datamodel Vulnerabilities Vulnerabilities search
```

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
