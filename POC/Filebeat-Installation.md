# FILEBEAT AIR GAPPED INSTALLATION ON LINUX

## Installation Steps ([Reference](https://www.elastic.co/docs/reference/beats/filebeat/filebeat-installation-configuration))

---

### 1.1 Get the Filebeat package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.1.2-linux-x86_64.tar.gz
```

### 1.2 Unzip the Elastic Agent package and Install as Fleet Server on Airgap-Server
```bash
sudo mkdir -p /usr/share/filebeat-9.1.2
sudo tar xzvf filebeat-9.1.2-linux-x86_64.tar.gz -C /usr/share/filebeat-9.1.2 --strip-components=1
```

---

### 2.1 Add Keystore for sensitive value

```bash
sudo /usr/share/filebeat-9.1.2/filebeat -c /usr/share/filebeat-9.1.2/filebeat.yml keystore add elasticsearch.username
```

```bash
sudo /usr/share/filebeat-9.1.2/filebeat -c /usr/share/filebeat-9.1.2/filebeat.yml keystore add elasticsearch.password
```

### 2.2 Configuration filebeat.yml

```bash
sudo nano /usr/share/filebeat-9.1.2/filebeat.yml
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
  hosts: ["https://10.143.120.98:9200"]
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

### 2.3 Test Config and Output
Test Config Command.
```bash
sudo /usr/share/filebeat-9.1.2/filebeat test config -c /usr/share/filebeat-9.1.2/filebeat.yml
```

The output should be like this.

```bash
Config OK
```

Test Output Command.
```bash
sudo /usr/share/filebeat-9.1.2/filebeat test output -c /usr/share/filebeat-9.1.2/filebeat.yml
```

The output should be like this.

```bash
elasticsearch: https://10.143.120.98:9200/...
  parse url... OK
  connection...
    parse host... OK
    dns lookup... OK
    addresses: 10.143.120.98
    dial up... OK
  TLS...
    security: server's certificate chain verification is enabled
    handshake... OK
    TLS version: TLSv1.3
    dial up... OK
  talk to server... OK
  version: 9.1.2
```

---

### 3.1 Setup service
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
ExecStart=/usr/share/filebeat-9.1.2/filebeat -e -c /usr/share/filebeat-9.1.2/filebeat.yml
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

### 3.2 Start Filebeat as a service
```bash
systemctl start filebeat.service
```
