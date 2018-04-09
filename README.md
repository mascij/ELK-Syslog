# How to Setup Free Centralize Logging of Syslog with Elastic Search, Kibana, and FileBeat

Instructions for setting up a ELK stack & monitoring Syslog for auditing usage and activity. This uses a partial ELK stack, ElasticSearch, Kibana, and FileBeat for shipping syslog from multiple Linux instances. Instructions are setup for running on Ubuntu 16.04. 

A central server runs ElasticSearch & Kibana, FileBeat is used on external Linux instances to monitor host activity.

## Step-by-step guide

#### Start with a fresh Ubuntu 16.04 Install
#### Run:

```sudo apt update

sudo apt install apt-transport-https```

