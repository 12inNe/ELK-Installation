# METRICBEAT AIR GAPPED INSTALLATION ON LINUX

## Installation Steps ([Reference](https://www.elastic.co/docs/reference/beats/metricbeat/metricbeat-installation-configuration))

### 1.1 Get the Metricbeat package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-9.1.2-linux-x86_64.tar.gz
```

---

### 1.2 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
tar xzvf metricbeat-9.1.2-linux-x86_64.tar.gz
cd metricbeat-9.1.2-linux-x86_64/
```

### 2.1 Config Metricbeat.yml

### 2.2 Enable Modules

#### 2.2.1 Elasticsearch

#### 2.2.2 Kibana

#### 2.2.3 Logstash
