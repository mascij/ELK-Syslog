# How to Setup Free Centralize Logging of Syslog with Elastic Search, Kibana, and FileBeat

Instructions for setting up a ELK stack & monitoring Syslog for auditing usage and activity. This uses a partial ELK stack, ElasticSearch, Kibana, and FileBeat for shipping syslog from multiple Linux instances. Instructions are setup for running on Ubuntu 16.04. 

A central server runs ElasticSearch & Kibana, FileBeat is used on external Linux instances to monitor host activity.

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

### Install ElasticSearch

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
