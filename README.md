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
