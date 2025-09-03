# FILEBEAT AIR GAPPED INSTALLATION ON LINUX

## Installation Steps ([Reference](https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration))

### 1.1 Get the Filebeat package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.1.2-linux-x86_64.tar.gz
```

---

### 1.2 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
tar xzvf filebeat-9.1.2-linux-x86_64.tar.gz
cd filebeat-9.1.2-linux-x86_64/
```

### 
