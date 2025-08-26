# ELASTIC AGENT AIR GAPPED INSTALLATION ON LINUX

## Requirement

- Elasticsearch
- Fleet Server

## Installation Steps

### 1.1 In Kibana, Open Fleet and add Fleet Server, Select name and choose Host.

### 1.2 Get the Elastic Agent package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-9.1.2-linux-x86_64.tar.gz
```
---

### 2.1 Generate CA and Fleet Server certificate signed by that CA
```bash
./bin/elasticsearch-certutil ca
./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
```
---

### 2.2 Export as PEM files and extract it
```vbnet
./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12 --pem
unzip certificate-bundle.zip -d fleet-server-certs
```

There should be this files in extracted directory.
```
instance.crt
instance.key
ca.crt
```
---

### 3.1 Re-Config Fleet Server
```yml
ssl:
  enabled: true
  certificate: /path/to/fleet-server-certs/instance.crt
  key: /path/to/fleet-server-certs/instance.key
  certificate_authorities: ["/path/to/fleet-server-certs/ca.crt"]
```
---

### 1.3 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
tar xzvf elastic-agent-9.1.2-linux-x86_64.tar.gz
cd elastic-agent-9.1.2-linux-x86_64
sudo ./elastic-agent enroll \
  --url=https://10.0.2.15:8220 \
  --enrollment-token=QXF1UDVKZ0ItNFAxVDJ4QXF6R3E6dWtUQWNmSXZUWWNPU0daWFMxT3I4Zw== \
  
```

<img width="893" height="370" alt="image" src="https://github.com/user-attachments/assets/72d35e01-7bf3-4d5c-b722-ab9a0a03e8bf" />
