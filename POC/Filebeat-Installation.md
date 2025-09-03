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

### 2.1 Add Keystore for sensitive value

```bash
./filebeat keystore add elasticsearch.username
```

```bash
./filebeat keystore add elasticsearch.password
```

### 2.2 Configuration filebeat.yml

```bash
nano filebeat.yml
```

```yaml
filebeat.inputs:
- type: filestream
  id: log-id
  enabled: true
  paths:
    - /path/to/syslog

filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml

  reload.enabled: false

output.elasticsearch:
  hosts: ["https://<ELASTIC_IP>:9200"]
  preset: balanced
  protocol: "https"
  username: "${elasticsearch.username}"
  password: "${elasticsearch.password}"
  ssl:
    enabled: true
    certificate_authorities: ["/path/to/http_ca.crt"]

processors:
  - add_host_metadata:
      when.not.contains.tags: forwarded
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~
```

### 3 Setup service
```bash
sudo nano /etc/systemd/system/filebeat.service
```

```yml
[Unit]
Description=Filebeat
Documentation=https://www.elastic.co/beats/filebeat
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=user
Group=user-group
WorkingDirectory=/filebeat/downloaded/path/filebeat-9.1.2-linux-x86_64
ExecStart=/filebeat/downloaded/path/filebeat-9.1.2-linux-x86_64/filebeat -e
Restart=always
TimeoutStopSec=20
LimitNOFILE=262144
PrivateTmp=true
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

###### Beware permission
