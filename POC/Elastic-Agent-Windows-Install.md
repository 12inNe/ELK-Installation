1. Run Powershell as Administrator

2. Run this command to download and install Elastic Agent also Connect to Fleet Server

```powershell
$ProgressPreference = 'SilentlyContinue'
Invoke-WebRequest -Uri https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-9.1.2-windows-x86_64.zip -OutFile elastic-agent-9.1.2-windows-x86_64.zip 
Expand-Archive .\elastic-agent-9.1.2-windows-x86_64.zip -DestinationPath .
cd elastic-agent-9.1.2-windows-x86_64
.\elastic-agent.exe install --url=https://10.0.2.15:8220 --enrollment-token=<1 token / agent / machine>
```

Token List :
```
YW5MY0Rwa0JiUUhnNzVlM3BsYms6TDlZcHNqT05xdzFDVXBLNlJDUlpvQQ==
```
```
S3EtUTZaZ0JVUjQ2azAyc0ZwY3Q6blJ2bVVsN2lIS2NBeWg3WEoxZDZ4UQ==
```
```
dW5RMkQ1a0JiUUhnNzVlM2dYLXE6ZGhPT01EQzByTTQ4TVlOeV9qdFBDdw==
```
