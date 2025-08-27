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
``` sudo nano /etc/systemd/system/logstash.service ```
And write these configs
```yaml
[Unit]
Description=Logstash
After=network.target elasticsearch.service

[Service]
Type=simple
User=ubuntuadm
Group=ubuntuadm
Environment=JAVA_HOME=/usr/share/logstash/jdk
ExecStart=/usr/share/logstash/bin/logstash --path.settings /etc/logstash
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```
# Step 3. Edit .yml files
## Add these lines
```yaml
path.data: /var/lib/logstash
path.logs: /var/log/logstash
pipeline.ordered: auto
```
# Step 4. Create pipeline files for testing
## etc/logstash/pipeline.conf 
```yaml
input {
  file {
    path => "/var/log/elasticsearch/*.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  mutate {
    add_field => {
      "log_type" => "elasticsearch"
      "service" => "elasticsearch"
    }
  }
}

output {
  elasticsearch {
    hosts => ["https://10.143.120.98:9200"]
    user => "elastic"
    password => "El@st1c"
    ssl_certificate_authorities => ["/etc/elasticsearch/certs/http_ca.crt"]
    ssl_verification_mode => "full"
    index => "log-test-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```
## Test pipeline config
```
/usr/share/logstash/bin/logstash --path.settings /etc/logstash -t
```
Should get
OK result

# Step 5. run services
```
sudo systemctl daemon-reload
sudo systemctl enable logstash
sudo systemctl start logstash
sudo systemctl status logstash
sudo journalctl -u logstash.service
```

# Step 6. Open Kibana and test the index and log creation
