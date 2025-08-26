# Step 1. Change files path
## At Dir elasticsearch-9.1.2
Create each directory and move the directories inside
```
sudo mkdir -p /etc/elasticsearch
sudo cp -r /home/user/ELK_package/elasticsearch-9.1.2/config/* /etc/elasticsearch/
sudo mkdir -p /usr/share/elasticsearch
sudo mv /home/user/ELK_package/elasticsearch-9.1.2/* /usr/share/elasticsearch/
sudo mkdir -p /var/lib/elasticsearch
sudo mkdir -p /var/log/elasticsearch
sudo chown -R youruser:yourgroup /var/lib/elasticsearch /var/log/elasticsearch
```
## Result layouts
```
/etc/kibana/          ← configuration
/usr/share/kibana/    ← binaries, node, plugins, etc.
/var/lib/kibana/      ← data
/var/log/kibana/      ← logs
```
# Step 2. Create service files
``` sudo nano /etc/systemd/system/elasticsearch.service ```
And write these configs
```yaml
[Unit]
Description=Elasticsearch
After=network.target

[Service]
Type=simple
User=ubuntuadm
Group=ubuntuadm
Environment=ES_PATH_CONF=/etc/elasticsearch
ExecStart=/usr/share/elasticsearch/bin/elasticsearch
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```
# Step 3. Edit .yml files
## [Example .yml config](https://github.com/12inNe/ELK-Installation/blob/main/POC/elasticsearch.yml)

# Step 4. run services
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
sudo journalctl -u elasticsearch.service
```

# Step 5. Set up the password and check the status
```
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
export ELASTIC_PASSWORD="your_password"

curl --cacert /etc/elasticsearch/certs/http_ca.crt \
     -u elastic:$ELASTIC_PASSWORD \
     https://localhost:9200
```



