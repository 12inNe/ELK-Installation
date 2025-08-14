# Installing Elasticsearch on RHEL/CentOS 9.x

## Step 1: Import the Elasticsearch PGP Key
```bash
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

## Step 2: Configure the Elasticsearch Repository
```bash
sudo tee /etc/yum.repos.d/elasticsearch.repo <<EOF
[elasticsearch]
name=Elasticsearch repository for 9.x packages
baseurl=https://artifacts.elastic.co/packages/9.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
EOF
```
Then Install Elasticsearch
```bash
sudo dnf install --enablerepo=elasticsearch elasticsearch
```
## Step 3: Edit .yml File for Multi-node Setup
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
```
### Setting up these varible
```bash
cluster.name: elasticsearch-demo
node.name: node-1
network.host: 0.0.0.0
cluster.initial_master_nodes: ["node-1"]
transport.host: 0.0.0.0
```
#### Don't forget to comment this part cluster.initial_master_nodes: ["localhost"]

## Step 4: Start/Stop and Auto-Start service
```bash
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo systemctl restart elasticsearch.service
```
## Step 5: Reset the elastic superuser password
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```
### If you want to create you own password add "-i"
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
```
### Recommend | stroring as Enviroment variable
```bash
export ELASTIC_PASSWORD="your_password"
```
## Step 6: Check that Elasticsearch is running
```bash
sudo cp /etc/elasticsearch/certs/http_ca.crt /tmp/http_ca.crt
sudo chmod 644 /tmp/http_ca.crt
curl --cacert /tmp/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200
```
### Example Output
```bash
{
  "name" : "node-1",
  "cluster_name" : "elasticsearch-demo",
  "cluster_uuid" : "eBiwxTIHRe2Ql11XD2qkzA",
  "version" : {
    "number" : "9.1.2",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "ca1a70216fbdefbef3c65b1dff04903ea5964ef5",
    "build_date" : "2025-08-11T15:04:41.449624592Z",
    "build_snapshot" : false,
    "lucene_version" : "10.2.2",
    "minimum_wire_compatibility_version" : "8.19.0",
    "minimum_index_compatibility_version" : "8.0.0"
  },
  "tagline" : "You Know, for Search"
}
```
