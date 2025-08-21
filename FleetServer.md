# FLEET SERVER AIR GAPPED INSTALLATION ON LINUX

## Installation Steps

### 1.1 In Kibana, Open Fleet and add Fleet Server, Select name and choose Host.

### 1.2 Get the Elastic Agent package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-9.1.2-linux-x86_64.tar.gz
```
---

### 1.3 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
tar xzvf elastic-agent-9.1.2-linux-x86_64.tar.gz
cd elastic-agent-9.1.2-linux-x86_64
sudo ./elastic-agent install \
  --fleet-server-es=https://10.0.2.15:9200 \
  --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3NTU3NzAzOTg0MjY6NkJuV2VEZTNUaHkzTEZjY2o5WXhkUQ \
  --fleet-server-policy=fleet-server-policy \
  --fleet-server-es-ca-trusted-fingerprint=572b43250385263cfb52b96513a237082dddc4269692ca4a5948ca724370c783 \
  --fleet-server-port=8220 \
  --install-servers \
  --force
```

### 2.1 Settings for the monitored host are configured in the [agent policy](https://www.elastic.co/docs/reference/fleet/agent-policy). Create a new agent policy to get started.

<img width="893" height="370" alt="image" src="https://github.com/user-attachments/assets/72d35e01-7bf3-4d5c-b722-ab9a0a03e8bf" />

