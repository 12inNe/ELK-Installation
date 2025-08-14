# Encrypt traffic between browser and Kibana

### Add Certificate for Kibana

1. Create Certificate

```sh
sudo /usr/share/elasticsearch/bin/elasticsearch-certutil csr -name kibana-server -ip 10.0.2.15
/kibana-server.zip #Filename can be anything
```

2. Extract zip file

```sh
sudo unzip /kibana-server.zip
```

3. Clean Up

```sh
sudo rm /kibana-server.zip
```

4. Submit the `kibana-server.csr` certificate signing request to your organization’s security team or certificate authority to obtain a signed certificate. The resulting certificate might be in different formats, such as a `.crt` file like `kibana-server.crt`.

5. Change config in `kibana.yml`
 Uncomment and change to your path

```sh
sudo nano /etc/kibana/kibana.yml
```

```yml
server.ssl.enabled: true
server.ssl.certificate: /kibana-server/kibana-server.crt
server.ssl.key: /kibana-server/kibana-server.key
``` 

6. Start Kibana

```sh
systemctl start kibana.service
```