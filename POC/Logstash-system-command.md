# Step 1. Change files path
## At Dir logstash-9.1.2
Create each directory and move the directories inside
```
sudo mkdir -p /etc/logstash /usr/share/logstash /var/lib/logstash /var/log/logstash
sudo cp -r ~/ELK_package/logstash-9.1.2/config/* /etc/logstash/
sudo cp -r ~/ELK_package/logstash-9.1.2/* /usr/share/logstash/
sudo chown -R ubuntuadm:ubuntuadm /etc/logstash /usr/share/logstash /var/lib/logstash /var/log/logstash
```

# Step 2. Create service files
``` sudo nano /etc/systemd/system/kibana.service ```
And write these configs
```yaml
[Unit]
Description=Kibana
After=network.target

[Service]
Type=simple
User=ubuntuadm
Group=ubuntuadm
Environment=KIBANA_HOME=/usr/share/kibana
ExecStart=/usr/share/kibana/bin/kibana -c /etc/kibana/kibana.yml
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```
# Step 3. Edit .yml files
## [Example .yml config](https://github.com/12inNe/ELK-Installation/blob/main/POC/kibana.yml)

# Step 4. Enrol Kiban in Elasticsearch
```
sudo ES_PATH_CONF=/etc/elasticsearch /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
eyJ2ZXIiOiI4LjE0LjAiLCJhZHIiOlsiMTAuMTQzLjEyMC45ODo5MjAwIl0sImZnciI6ImY4MzBhMGY4NGI3NjEwZjYxZDA1YjhjNzZjMzcxYzM2ZDY3ZWFkNDI3YjRlNWJhZTU4YmJjOTJiOTJjMzAzNDMiLCJrZXkiOiJzd3NsNVpnQlc0SFhFRTJnckNIYTpCS0JJcm1JT0dLdjdZQ1lnSlJoSlF3In0=

sudo /usr/share/kibana/bin/kibana-setup --enrollment-token
eyJ2ZXIiOiI4LjE0LjAiLCJhZHIiOlsiMTAuMTQzLjEyMC45ODo5MjAwIl0sImZnciI6ImY4MzBhMGY4NGI3NjEwZjYxZDA1YjhjNzZjMzcxYzM2ZDY3ZWFkNDI3YjRlNWJhZTU4YmJjOTJiOTJjMzAzNDMiLCJrZXkiOiJzd3NsNVpnQlc0SFhFRTJnckNIYTpCS0JJcm1JT0dLdjdZQ1lnSlJoSlF3In0=
```
## Result
```
Native global console methods have been overridden in production environment.

✔ Kibana configured successfully.

To start Kibana run:
  bin/kibana
```
# Step 5. run services
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
sudo journalctl -u elasticsearch.service
```

# Step 6. Open Kibana
Open Kibana via [http://10.143.120.98:5601/](http://10.143.120.98:5601/)
