# SPLUNK

## Versions Supported

This integration is last tested with Artifactory 7.71.11 and Xray 3.88.12 versions.

## Table of Contents

`Note! You must follow the order of the steps throughout Splunk Configuration`
1. [Splunk Setup](#splunk-setup)
2. [JFrog Metrics Setup](#jfrog-metrics-setup)
3. [Fluentd Installation](#fluentd-installation)
    * [OS / Virtual Machine](#os--virtual-machine)
    * [Docker](#docker)
    * [Kubernetes Deployment with Helm](#kubernetes-deployment-with-helm)
4. [Dashboards](#dashboards)
5. [Splunk Demo](#splunk-demo)
6. [References](#references)

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
5. Select Index Data Type as Metrics
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
8. Add "jfrog_splunk" index to store the JFrog platform log data into.
9. Click on the green "Review" button
10. If good, Click on the green "Done" button
11. Save the generated token value
````

#### Configure new HEC token to receive Metrics
````text
1. Open Splunk web console as administrator
2. Click on "Settings" in dropdown select "Data inputs"
3. Click on "HTTP Event Collector"
4. Click on "New Token"
5. Enter a "Name" in the textbox
6. (Optional) Enter a "Description" in the textbox
7. Click on the green "Next" button
8. Add "jfrog_splunk_metrics" index to store the JFrog platform metrics data into.
9. Click on the green "Review" button
10. If good, Click on the green "Done" button
11. Save the generated token value
````

## JFrog Metrics Setup
To enable metrics in Artifactory, make the following configuration changes to the [Artifactory System YAML](https://www.jfrog.com/confluence/display/JFROG/Artifactory+System+YAML)
```yaml
artifactory:
    metrics:
        enabled: true
    openMetrics:
        enabled: true
```
Once this configuration is done and the application is restarted, metrics will be available in Open Metrics Format

Metrics are enabled by default in Xray.
For kubernetes based installs, openMetrics are enabled in the helm install commands listed below

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

````shell
gem install fluent-plugin-concat
gem install fluent-plugin-splunk-hec
gem install fluent-plugin-jfrog-siem
gem install fluent-plugin-jfrog-metrics
````

#### Configure Fluentd
We rely heavily on environment variables so that the correct log files are streamed to your observability dashboards. Ensure that you fill in the .env file with correct values. Download the .env file from [here](https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/jfrog.env)

* **JF_PRODUCT_DATA_INTERNAL**: The environment variable JF_PRODUCT_DATA_INTERNAL must be defined to the correct location. For each JFrog service you will find its active log files in the `$JFROG_HOME/<product>/var/log` directory
* **SPLUNK_COM_PROTOCOL**: HTTP Scheme, http or https
* **SPLUNK_HEC_HOST**: Splunk Instance URL
* **SPLUNK_HEC_PORT**: Splunk HEC configured port
* **SPLUNK_HEC_TOKEN**: Splunk HEC Token for sending logs to Splunk
* **SPLUNK_METRICS_HEC_TOKEN**: Splunk HEC Token for sending metrics to Splunk
* **SPLUNK_INSECURE_SSL**: false for test environments only or if http scheme. 
* **JPD_URL**: Artifactory JPD URL of the format `http://<ip_address>`
* **JPD_ADMIN_USERNAME**: Artifactory username for authentication
* **JFROG_ADMIN_TOKEN**: Artifactory [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) for authentication
* **COMMON_JPD**: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

Apply the .env files and then run the fluentd wrapper with one argument pointed to the `fluent.conf.*` file configured.

````shell
source jfrog.env
./fluentd $JF_PRODUCT_DATA_INTERNAL/fluent.conf.<product_name>
````

### Docker
`Note! These steps were not tested to work out of the box on MAC`
In order to run fluentd as a docker image to send the logs, violations and metrics data to splunk, the following commands needs to be executed on the host that runs the docker.

1. Check the docker installation is functional, execute command 'docker version' and 'docker ps'.

2. Once the version and process are listed successfully, build the intended docker image for Splunk using the docker file,

	* Download Dockerfile from [here](https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/docker-build/Dockerfile) to any directory which has write permissions.

3. Download the docker.env file needed to run Jfrog/FluentD Docker Images for Splunk,

	* Download docker.env from [here](https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/docker-build/docker.env) to the directory where the docker file was downloaded.

```text

For Splunk as the observability platform, execute these commands to setup the docker container running the fluentd installation

1. Execute 'docker build --build-arg SOURCE="JFRT" --build-arg TARGET="SPLUNK" -t <image_name> .'

    Command example

    'docker build --build-arg SOURCE="JFRT" --build-arg TARGET="SPLUNK" -t jfrog/fluentd-splunk-rt .'

    The above command will build the docker image.

2. Fill the necessary information in the docker.env file

    JF_PRODUCT_DATA_INTERNAL: The environment variable JF_PRODUCT_DATA_INTERNAL must be defined to the correct location. For each JFrog service you will find its active log files in the `$JFROG_HOME/<product>/var/log` directory
    SPLUNK_COM_PROTOCOL: HTTP Scheme, http or https
    SPLUNK_HEC_HOST: Splunk Instance URL
    SPLUNK_HEC_PORT: Splunk HEC configured port
    SPLUNK_HEC_TOKEN: Splunk HEC Token for sending logs to Splunk
    SPLUNK_METRICS_HEC_TOKEN: Splunk HEC Token for sending metrics to Splunk
    SPLUNK_INSECURE_SSL: false for test environments only or if http scheme
    JPD_URL: Artifactory JPD URL of the format `http://<ip_address>`
    JPD_ADMIN_USERNAME: Artifactory username for authentication
    JFROG_ADMIN_TOKEN: Artifactory [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) for authentication
    COMMON_JPD: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

3. Execute 'docker run -it --name jfrog-fluentd-splunk-rt -v <path_to_logs>:/var/opt/jfrog/artifactory --env-file docker.env <image_name>' 

    The <path_to_logs> should be an absolute path where the Jfrog Artifactory Logs folder resides, i.e for an Docker based Artifactory Installation,  ex: /var/opt/jfrog/artifactory/var/logs on the docker host.

    Command example

    'docker run -it --name jfrog-fluentd-splunk-rt -v $JFROG_HOME/artifactory/var/:/var/opt/jfrog/artifactory --env-file docker.env jfrog/fluentd-splunk-rt'


```

### Kubernetes Deployment with Helm
Recommended installation for Kubernetes is to utilize the helm chart with the associated values.yaml in this repo.

| Product        | Example Values File             |
|----------------|---------------------------------|
| Artifactory    | helm/artifactory-values.yaml    |
| Artifactory HA | helm/artifactory-ha-values.yaml |
| Xray           | helm/xray-values.yaml           |

> [!WARNING]
> 
> The old docker registry `partnership-pts-observability.jfrog.io`, which contains older versions of this integration is now deprecated. We'll keep the existing docker images on this old registry until August 1st, 2024. After that date, this registry will no longer be available. Please `helm upgrade` your JFrog kubernetes deployment in order to pull images as specified on the above helm value files, from the new `releases-pts-observability-fluentd.jfrog.io` registry. Please do so in order to avoid `ImagePullBackOff` errors in your deployment once this registry is gone.

Add JFrog Helm repository:

```shell
helm repo add jfrog https://charts.jfrog.io
helm repo update
```
Replace placeholders with your ``masterKey`` and ``joinKey``. To generate each of them, use the command
``openssl rand -hex 32``

#### Artifactory ⎈:

1. Skip this step if you already have Artifactory installed. Else, install Artifactory using the command below
    ```shell
    helm upgrade --install artifactory  jfrog/artifactory \
           --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
           --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
           --set artifactory.license.secret=artifactory-license \
           --set artifactory.license.dataKey=artifactory.cluster.license \
           --set artifactory.metrics.enabled=true \
           --set artifactory.openMetrics.enabled=true
    ```

2. Create a secret for JFrog's admin token - [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) using any of the following methods
    ```shell
    kubectl create secret generic jfrog-admin-token --from-file=token=<path_to_token_file>
    
    OR
    
    kubectl create secret generic jfrog-admin-token --from-literal=token=<JFROG_ADMN_TOKEN>
    ```
3. For Artifactory installation, download the .env file from [here](https://github.com/jfrog/log-analytics-splunk/raw/master/helm/jfrog_helm.env). Fill in the jfrog_helm.env file with correct values.

   * **SPLUNK_COM_PROTOCOL**: HTTP Scheme, http or https
   * **SPLUNK_HEC_HOST**: Splunk Instance URL
   * **SPLUNK_HEC_PORT**: Splunk HEC configured port
   * **SPLUNK_HEC_TOKEN**: Splunk HEC Token for sending logs to Splunk
   * **SPLUNK_METRICS_HEC_TOKEN**: Splunk HEC Token for sending metrics to Splunk
   * **SPLUNK_INSECURE_SSL**: false for test environments only or if http scheme
   * **JPD_URL**: Artifactory JPD URL of the format `http://<ip_address>`
   * **JPD_ADMIN_USERNAME**: Artifactory username for authentication
   * **COMMON_JPD**: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

    Apply the .env files using the helm command below

    ````shell
    source jfrog_helm.env
    ````

4. Postgres password is required to upgrade Artifactory. Run the following command to get the current password
   ```shell
   POSTGRES_PASSWORD=$(kubectl get secret artifactory-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)
   ```

5. Upgrade Artifactory installation using the command below
    ```shell
    helm upgrade --install artifactory jfrog/artifactory \
           --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
           --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
           --set artifactory.metrics.enabled=true --set artifactory.openMetrics.enabled=true \
           --set databaseUpgradeReady=true --set postgresql.postgresqlPassword=$POSTGRES_PASSWORD --set nginx.service.ssloffload=true \
           --set splunk.host=$SPLUNK_HEC_HOST \
           --set splunk.port=$SPLUNK_HEC_PORT \
           --set splunk.logs_token=$SPLUNK_HEC_TOKEN \
           --set splunk.metrics_token=$SPLUNK_METRICS_HEC_TOKEN \
           --set splunk.com_protocol=$SPLUNK_COM_PROTOCOL \
           --set splunk.insecure_ssl=$SPLUNK_INSECURE_SSL \
           --set jfrog.observability.jpd_url=$JPD_URL \
           --set jfrog.observability.username=$JPD_ADMIN_USERNAME \
           --set jfrog.observability.common_jpd=$COMMON_JPD \
           -f helm/artifactory-values.yaml
    ```

#### Artifactory-HA ⎈:
1. For HA installation, please create a license secret on your cluster prior to installation.
   ```shell
   kubectl create secret generic artifactory-license --from-file=<path_to_license_file>artifactory.cluster.license 
   ```
2. Skip this step if you already have Artifactory installed. Else, install Artifactory using the command below
    ```shell
    helm upgrade --install artifactory-ha  jfrog/artifactory-ha \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       --set artifactory.license.secret=artifactory-license \
       --set artifactory.license.dataKey=artifactory.cluster.license \
       --set artifactory.metrics.enabled=true \
       --set artifactory.openMetrics.enabled=true
    ```
3. Create a secret for JFrog's admin token - [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) using any of the following methods
   ```shell
   kubectl create secret generic jfrog-admin-token --from-file=token=<path_to_token_file>
   
   OR
   
   kubectl create secret generic jfrog-admin-token --from-literal=token=<JFROG_ADMN_TOKEN>
   ```
4. Download the .env file from [here](https://github.com/jfrog/log-analytics-splunk/raw/master/helm/jfrog_helm.env). Fill in the jfrog_helm.env file with correct values.

   * **SPLUNK_COM_PROTOCOL**: HTTP Scheme, http or https
   * **SPLUNK_HEC_HOST**: Splunk Instance URL
   * **SPLUNK_HEC_PORT**: Splunk HEC configured port
   * **SPLUNK_HEC_TOKEN**: Splunk HEC Token for sending logs to Splunk
   * **SPLUNK_METRICS_HEC_TOKEN**: Splunk HEC Token for sending metrics to Splunk
   * **SPLUNK_INSECURE_SSL**: false for test environments only or if http scheme
   * **JPD_URL**: Artifactory JPD URL of the format `http://<ip_address>`
   * **JPD_ADMIN_USERNAME**: Artifactory username for authentication
   * **COMMON_JPD**: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

   Apply the .env files and then run the helm command below

   ````shell
   source jfrog_helm.env
   ````
5. Postgres password is required to upgrade Artifactory. Run the following command to get the current password
   ```shell
   POSTGRES_PASSWORD=$(kubectl get secret artifactory-ha-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)
   ```
6. Upgrade Artifactory HA installation using the command below
   ```text
   helm upgrade --install artifactory-ha  jfrog/artifactory-ha \
       --set artifactory.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set artifactory.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE --set artifactory.replicaCount=0 \
       --set artifactory.metrics.enabled=true --set artifactory.openMetrics.enabled=true \
       --set databaseUpgradeReady=true --set postgresql.postgresqlPassword=$POSTGRES_PASSWORD --set nginx.service.ssloffload=true \
       --set splunk.host=$SPLUNK_HEC_HOST \
       --set splunk.port=$SPLUNK_HEC_PORT \
       --set splunk.logs_token=$SPLUNK_HEC_TOKEN \
       --set splunk.metrics_token=$SPLUNK_METRICS_HEC_TOKEN \
       --set splunk.com_protocol=$SPLUNK_COM_PROTOCOL \
       --set splunk.insecure_ssl=$SPLUNK_INSECURE_SSL \
       --set jfrog.observability.jpd_url=$JPD_URL \
       --set jfrog.observability.username=$JPD_ADMIN_USERNAME \
       --set jfrog.observability.common_jpd=$COMMON_JPD \
       -f helm/artifactory-ha-values.yaml
   ```

#### Xray ⎈:

Create a secret for JFrog's admin token - [Access Token](https://jfrog.com/help/r/how-to-generate-an-access-token-video/artifactory-creating-access-tokens-in-artifactory) using any of the following methods if it doesn't exist
```shell
kubectl create secret generic jfrog-admin-token --from-file=token=<path_to_token_file>

OR

kubectl create secret generic jfrog-admin-token --from-literal=token=<JFROG_ADMN_TOKEN>
```

For Xray installation, download the .env file from [here](https://raw.githubusercontent.com/jfrog/log-analytics-splunk/master/jfrog_helm.env). Fill in the jfrog_helm.env file with correct values.

* **SPLUNK_COM_PROTOCOL**: HTTP Scheme, http or https
* **SPLUNK_HEC_HOST**: Splunk Instance URL
* **SPLUNK_HEC_PORT**: Splunk HEC configured port
* **SPLUNK_HEC_TOKEN**: Splunk HEC Token for sending logs to Splunk
* **SPLUNK_METRICS_HEC_TOKEN**: Splunk HEC Token for sending metrics to Splunk
* **SPLUNK_INSECURE_SSL**: false for test environments only or if http scheme
* **JPD_URL**: Artifactory JPD URL of the format `http://<ip_address>`
* **JPD_ADMIN_USERNAME**: Artifactory username for authentication
* **JFROG_ADMIN_TOKEN**: For security reasons, this value will be pulled from the secret jfrog-admin-token created in the step above
* **COMMON_JPD**: This flag should be set as true only for non-kubernetes installations or installations where JPD base URL is same to access both Artifactory and Xray (ex: https://sample_base_url/artifactory or https://sample_base_url/xray)

Apply the .env files and then run the helm command below

````shell
source jfrog_helm.env
````

Use the same `joinKey` as you used in Artifactory installation to allow Xray node to successfully connect to Artifactory.

```shell
helm upgrade --install xray jfrog/xray --set xray.jfrogUrl=http://my-artifactory-nginx-url \
       --set xray.masterKey=FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF \
       --set xray.joinKey=EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE \
       --set splunk.host=$SPLUNK_HEC_HOST \
       --set splunk.port=$SPLUNK_HEC_PORT \
       --set splunk.logs_token=$SPLUNK_HEC_TOKEN \
       --set splunk.metrics_token=$SPLUNK_METRICS_HEC_TOKEN \
       --set splunk.com_protocol=$SPLUNK_COM_PROTOCOL \
       --set splunk.insecure_ssl=$SPLUNK_INSECURE_SSL \
       --set jfrog.observability.jpd_url=$JPD_URL \
       --set jfrog.observability.username=$JPD_ADMIN_USERNAME \
       --set jfrog.observability.common_jpd=$COMMON_JPD \
       -f helm/xray-values.yaml
```

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

This will create a new Splunk instance that can be used for a demo to send JFrog logs, violations and metrics over to. Follow the setup steps listed above to see data in the Dashboards.

## References

* [Fluentd](https://www.fluentd.org) - Fluentd Logging Aggregator/Agent
* [Splunk](https://www.splunk.com/) - Splunk Logging Platform
* [Splunk HEC](https://dev.splunk.com/enterprise/docs/dataapps/httpeventcollector/) - Splunk HEC used to upload data into Splunk
