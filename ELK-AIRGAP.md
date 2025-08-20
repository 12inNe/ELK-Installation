# ELK-AIRGAP INSTALLATION ON LINUX
## Pre-Requisite Please Check in 
[Matrix Support](https://www.elastic.co/support/matrix) AND [System Configs](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/important-system-configuration)

## Installation Steps
### 1.1 Get the Elasticsearch package on a Host with Internet
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.2-linux-x86_64.tar.gz.sha512
sha512sum -c elasticsearch-9.1.2-linux-x86_64.tar.gz.sha512
```
### 1.2 Unzip the Elasticsearch package on Airgap-Server
```bash
tar -xzf elasticsearch-9.1.2-linux-x86_64.tar.gz
cd elasticsearch-9.1.2/
```
