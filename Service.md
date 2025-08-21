# Make ELK Stack run as a service (Air Gapped)

### Example (Master Hot Node)
```service
[Unit]
Description=Elasticsearch
Documentation=https://www.elastic.co
Wants=network-online.target
After=network-online.target

[Service]
RuntimeDirectory=pichanmet
PrivateTmp=true
Type=simple
User=pichanmet
Group=pichanmet
ExecStart=/home/pichanmet/elasticsearch-9.1.2/bin/elasticsearch
StandardOutput=journal
StandardError=journal
LimitNOFILE=262144
TimeoutStopSec=20
Restart=always

[Install]
WantedBy=multi-user.target
```

### For Metricbeat
```service
[Unit]
Description=Elasticsearch
Documentation=https://www.elastic.co
Wants=network-online.target
After=network-online.target

[Service]
RuntimeDirectory=root
PrivateTmp=true
Type=simple
User=pichanmet
Group=pichanmet
WorkingDirectory=/home/pichanmet/metricbeat-9.1.2-linux-x86_64
ExecStart=/home/pichanmet/metricbeat-9.1.2-linux-x86_64/metricbeat -e -c /home/pichanmet/metricbeat-9.1.2-linux-x86_64/metricbeat.yml
StandardOutput=journal
StandardError=journal
LimitNOFILE=262144
TimeoutStopSec=20
Restart=always

[Install]
WantedBy=multi-user.target
```
