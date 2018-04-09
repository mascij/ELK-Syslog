# How to Setup Free Centralize Logging of Syslog with Elastic Search, Kibana, and FileBeat

Instructions for setting up a ELK stack & monitoring Syslog for auditing usage and activity. This uses a partial ELK stack, ElasticSearch, Kibana, and FileBeat for shipping syslog from multiple Linux instances. Instructions are setup for running on Ubuntu 16.04. 

A central server runs ElasticSearch & Kibana, FileBeat is used on external Linux instances to monitor host activity.

![alt text](https://i.imgur.com/NJgvcvV.png)

## Step-by-step guide

#### Start with a fresh Ubuntu 16.04 Install
#### Run:

```
sudo apt update

sudo apt install apt-transport-https
```

## Install Java
#### Install Java with Apt

```
Sudo apt install default-jre
```

### Set Java Home:

#### Find the install path:

`sudo update-alternatives --config java`

#### Edit the environment variables:
`sudo nano /etc/environment`

#### Add: 

`JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`

#### Reload the file:

`source /etc/environment`

## Install ElasticSearch

https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html

#### Download & install public key:

`wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`

#### Save the report to sources list:

`echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list`

#### Install with apt:

`sudo apt update && sudo apt install elasticsearch`

#### Edit ElasticSearch Config File:

`sudo vi /etc/elasticsearch/elasticsearch.yml`

#### Set cluster name & network.host (to local IP):
![alt text](https://i.imgur.com/iYIf4ol.png)

#### Install Plugins for Syslog Monitoring for ElasticSearch

```
cd /usr/share/elasticsearch/bin
sudo ./elasticsearch-plugin install ingest-geoip
sudo ./elasticsearch-plugin install ingest-user-agent
```

#### Restart service:

```
sudo service elasticsearch restart
sudo service elasticsearch status
```

## Install Kibana
https://www.elastic.co/guide/en/kibana/current/deb.html

#### Download & install public key:

`wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`


#### Save the report to sources list:

`echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list`

#### Install with apt:
```
sudo apt update && sudo apt install kibana
Edit Config
cd /etc/kibana
sudo vi kibana.yml
```

#### Add server port, server.host, and elasticsearch server information

#### Restart Service & Use Web Browser to Validate
```
sudo service kibana restart
sudo service kibana status
```
#### User a web browser to check if 10.0.8.15:5601 is loaded & working

![alt text](https://i.imgur.com/V0lDmzF.png)

## Install FileBeat on the HCP Node
Next, install FileBeat on the Linux instance you would like to monitor

#### Download and install the Public Signing Key:

`wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`

#### Install apt-transport-https:

`sudo apt-get install apt-transport-https`

#### Save the repo definition:

`echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list`

#### Install FileBeat with apt:

`sudo apt update && apt install filebeat`

#### Edit Config:
```
cd /etc/filebeat/
sudo mv filebeat.yml filebeat.bak
sudo vi filebeat.yml
```

#### Paste the Following:
```
filebeat.prospectors:
#add prospectors for additional logging
output.elasticsearch:
  hosts: ["http://10.0.8.15:9200"]
  protocol: "http"
setup.kibana:
  host: "10.0.8.15:5601"
filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
```

![alt text](https://i.imgur.com/tF6Jtx1.png)

#### Perform the Initial Setup & Enable System Module:
```
cd /usr/share/filebeat/bin
./filebeat -e --modules system
```
Confirm there are no errors:

#### Install the Default Dashboards (only need to do this the first time you setup Kibana):

`./filebeat setup --dashboards`

#### Enable System Module

`sudo filebeat modules enable system`

#### Edit the System Module File to Include Syslog
```
cd /etc/filebeat/modules.d
sudo vi system.yml
```

#### Make it look like this:
```
- module: system
# Syslog
syslog:
enabled: true

# Set custom paths for the log files. If left empty,
# Filebeat will choose the paths depending on your OS.
var.paths: ["var/log/syslg"]

auth:
enabled: true
```

#### Restart service:
```
sudo service filebeat restart
sudo service filebeat status
```
## Setup FileBeat Index Pattern

Connect to you Kibana instance. Click Management on the left menu, then 'Index Patterns'. 
Type in `'filebeat-*'`

![alt text](https://i.imgur.com/m8avg5E.png)

![alt text](https://i.imgur.com/XSVLA8x.png)

## Access the Syslog Dashboard

Click Dashboards on the left menu, then '[FileBeat System] Syslog Dashboard'

And be sure to set an appropriate time range in the top right corner. You'll now see all the syslog events for all your hosts & have the ability to filter them by hostname, syslog program, and more. 

Check out the other syslog dashboards so you can find out about open SSH ports (attack attemps and malicious login attempts). 

![alt text](https://i.imgur.com/p9p0fL9.png)

Reach out to me if you have any questions on twitter with @mascij

Enjoy!






