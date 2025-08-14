# Encrypt traffic between browser and Kibana

### Add Cert for Kibana

```sh
sudo /usr/share/elasticsearch/bin/elasticsearch-certutil csr -name kibana-server -ip 10.0.2.15
/kibana-server.zip
```

Extract

```sh
sudo unzip /kibana-server.zip
```

Clean Up

```sh
sudo rm /kibana-server.zip
```