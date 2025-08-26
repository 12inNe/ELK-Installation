# ELASTIC AGENT AIR GAPPED INSTALLATION ON LINUX

## Requirement

- Elasticsearch
- Fleet Server

## Installation Steps

### 1.1 In Kibana, Open Fleet and add Agent, Select agent policy and enroll with fleet.

<img width="840" height="223" alt="image" src="https://github.com/user-attachments/assets/dcfbbe7d-33a1-4515-9301-b931a551a925" />

### 1.2 Get the Elastic Agent package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-9.1.2-linux-x86_64.tar.gz
```

### 1.3 Unzip the Elastic Agent package and Enroll Elastic Agent on Airgap-Server
```bash
tar xzvf elastic-agent-9.1.2-linux-x86_64.tar.gz
cd elastic-agent-9.1.2-linux-x86_64
sudo ./elastic-agent enroll \
  --url=https://10.0.2.15:8220 \
  --enrollment-token=<token> \
  --certificate-authorities=/home/pichanmet/elasticsearch-9.1.2/fleet-server-certs/ca.crt
```
