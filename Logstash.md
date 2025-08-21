# LOGSTASH AIR GAPPED INSTALLATION ON LINUX
## Pre-Requisite Please Check in

## Installation Steps

### 1.1 Get the Logstash package on a Host (This step require Internet, Skip this if host already have the package)

```bash
curl -O https://artifacts.elastic.co/downloads/logstash/logstash-9.1.2-linux-x86_64.tar.gz
curl https://artifacts.elastic.co/downloads/logstash/logstash-9.1.2-linux-x86_64.tar.gz.sha512 | sha512sum -c -
```
---

### 1.2 Unzip the Logstash package on Airgap-Server
```bash
tar -xzf logstash-9.1.2-linux-x86_64.tar.gz
cd logstash-9.1.2/
```

### 2.1 Create Pipeline Config
```bash
nano config/pipeline.conf
```

```conf
input {
  file {
    path => "/home/pichanmet/elasticsearch-9.1.2/hot-node/logs/*.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  grok {
    match => { "message" => "%{WORD:word} %{NUMBER:number}" }
  }
  mutate {
    convert => { "number" => "integer" }
  }
}

output {
  elasticsearch {
    hosts => ["https://10.0.2.15:9200"]
    user => "elastic"
    password => "a123456"
    ssl_verification_mode => "full"
    ssl_certificate_authorities => "/etc/elasticsearch/certs/http_ca.crt"
    index => "logs-stash"
  }
  stdout { codec => rubydebug }
}
```

### 2.2 Add pipeline to `logstash.yml`
```yml
- pipeline.id: main
  path.config: "./pipeline.conf"
```

### 2.3 Add api ip and port
```yml
api.enabled: true
api.http.host: 10.0.2.15
api.http.port: 9600
```

### Extra | Use keystore to store sensitive data like elasticsearch username and password

### Start Logstash Command
```bash
./bin/logstash
```
