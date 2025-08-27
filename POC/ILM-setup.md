# 1. Activate the license for using Snapshot repositories and Frozen tier
You can activate in Kibana or run this ```command curl -X POST "localhost:9200/_license/start_trial?acknowledge=true"```

# 2. Create Snapshot repositories 
```
sudo mkdir -p /var/elasticsearch-snapshots
sudo chown -R ubuntuadm:ubuntuadm /var/elasticsearch-snapshots
```

# 3. Edit ES config 
```sudo nano /etc/elasticsearch/elasticsearch.yml```

Add the below config and restart the service
```ymal 
path.repo: ["/opt/elasticsearch-snapshots"]
```
```
sudo systemctl restart elasticsearch
sudo systemctl status elasticsearch
```
# 4. Register repository in Kibana
### Use shared file system and set the location at /var/elasticsearch-snapshots
### after that, verify and clean up in Kibana

# 5. Create Policy in Kibana
### This depends on the usage you want 
### Run the Policy, and the snapshot will be created

