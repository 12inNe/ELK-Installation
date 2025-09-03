# Synthetic Test (Using Kibana UI)
# Type
- Multistep
- Singlepage
- HTTP
- ICMP
- TCP

## Multistep [Require Elasticsearch Docker Image]
###### Monitor your website via script that can simulate user, made automation setup test for multiple service in the website. 

## Singlepage [Require Elasticsearch Docker Image]
###### Monitor your website. Check page is loading and has correct text.

## HTTP
###### Monitor your website. The HTTP monitor checks to make sure specific endpoints return the correctstatus code and display the correct text.

### 1. Open [Kibana UI] -> [Observability] -> [Synthetic Monitoring] -> [Create Monitor] -> [Select HTTP Ping]

### 2. Setup ENDPOINT:PORT

#### Certificate
- Run `openssl pkcs12 -in config/certs/http.p12 -clcerts -nokeys -out client-cert.pem` to 

## ICMP
###### Check the availability of your hosts. The ICMP monitor uses ICMP (v4 and v6) EchoRequests to check the network reachability of the hosts you are pinging. This will tell you whether thehost is available and connected to the network, but doesn’t tell you if a service on the host is running or not.

### 1. Open [Kibana UI] -> [Observability] -> [Synthetic Monitoring] -> [Create Monitor] -> [Select ICMP Ping]

### 2. Setup HOST

## TCP
###### Monitor the services running on your hosts. The TCP monitor checks individual portsto make sure the service is accessible and running.

### 1. Open [Kibana UI] -> [Observability] -> [Synthetic Monitoring] -> [Create Monitor] -> [Select TCP Ping]

### 2. Setup HOST:PORT like this

<img width="1629" height="801" alt="image" src="https://github.com/user-attachments/assets/e7363e03-72f4-41c4-83f3-e9cc316a99bb" />

