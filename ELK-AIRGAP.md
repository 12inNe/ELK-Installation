# ELK-AIRGAP INSTALLATION ON LINUX
## Pre-Requisite Please Check in 
[Matrix Support](https://www.elastic.co/support/matrix) AND [System Configs](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/important-system-configuration)

## Host VM with internet
### 1. Elasticsearch installation
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.2-linux-x86_64.tar.gz.sha512
sha512sum -c elasticsearch-9.1.2-linux-x86_64.tar.gz.sha512
```
