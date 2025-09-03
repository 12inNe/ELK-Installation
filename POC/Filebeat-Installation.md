# FILEBEAT AIR GAPPED INSTALLATION ON LINUX

## Installation Steps ([Reference](https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration))

### 1.1 Get the Filebeat package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.1.2-linux-x86_64.tar.gz
```

---

### 1.2 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
sudo mkdir -p /usr/share/filebeat-9.1.2
sudo tar xzvf filebeat-9.1.2-linux-x86_64.tar.gz -C /usr/share/filebeat-9.1.2 --strip-components=1
```

### 2.1 Add Keystore for sensitive value

```bash
/usr/share/filebeat-9.1.2/filebeat keystore add elasticsearch.username
```

```bash
/usr/share/filebeat-9.1.2/filebeat keystore add elasticsearch.password
```

### 2.2 Configuration filebeat.yml

```bash
nano /usr/share/filebeat-9.1.2/filebeat.yml
```

```yaml
filebeat.inputs:
- type: filestream
  id: log-id
  enabled: true
  paths:
    - /path/to/syslog

filebeat.config.modules:
  path: /usr/share/filebeat-9.1.2/modules.d/*.yml

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

```service
[Unit]
Description=Filebeat
Documentation=https://www.elastic.co/beats/filebeat
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=YOUR_USER
Group=YOUR_USER_GROUP
WorkingDirectory=/usr/share/filebeat-9.1.2/
ExecStart=/usr/share/filebeat-9.1.2/filebeat -e
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
