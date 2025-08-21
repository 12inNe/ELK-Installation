# LOGSTASH AIR GAPPED INSTALLATION ON LINUX
## Pre-Requisite Please Check in

## Installation Steps

### 1.1 Get the Logstash package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -O https://artifacts.elastic.co/downloads/logstash/logstash-9.1.2-linux-x86_64.tar.gz
curl https://artifacts.elastic.co/downloads/logstash/logstash-9.1.2-linux-x86_64.tar.gz.sha512 | sha512sum -c -
```
---

### 1.2 Unzip the Logstash package on Airgap-Server
```bash
tar -xzf logstash-9.1.2-linux-x86_64.tar.gz
cd logstash-9.1.2/
```

### Start Logstash Command
```bash
./bin/logstash
```
