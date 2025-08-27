# Implementation Fleet Server

## Step 1
#### Generate Encryption Key in Kibana
```bash
/usr/share/kibana/bin/kibana-encryption-keys generate
```

#### Output should be like this
```bash
## Kibana Encryption Key Generation Utility

The 'generate' command guides you through the process of setting encryption keys for:

xpack.encryptedSavedObjects.encryptionKey
    Used to encrypt stored objects such as dashboards and visualizations
    https://www.elastic.co/guide/en/kibana/current/xpack-security-secure-saved-objects.html#xpack-security-secure-saved-objects

xpack.reporting.encryptionKey
    Used to encrypt saved reports
    https://www.elastic.co/guide/en/kibana/current/reporting-settings-kb.html#general-reporting-settings

xpack.security.encryptionKey
    Used to encrypt session information
    https://www.elastic.co/guide/en/kibana/current/security-settings-kb.html#security-session-and-cookie-settings


Already defined settings are ignored and can be regenerated using the --force flag.  Check the documentation links for instructions on how to rotate encryption keys.
Definitions should be set in the kibana.yml used configure Kibana.

Settings:
xpack.encryptedSavedObjects.encryptionKey: c89682cb740d28a00ecbbf31928d48db
xpack.reporting.encryptionKey: 979d5d60a3ea570fb1b62395464def93
xpack.security.encryptionKey: 2b73583647548cff71818d0fe7369d96
```

Put the 3 final lines in kibana.yml then restart Kibana Service

## Step 2
#### Add Fleet Server and do a basic Config

<img width="949" height="798" alt="image" src="https://github.com/user-attachments/assets/a8393aa4-37eb-4eeb-8118-092e43bb6a69" />

<img width="909" height="287" alt="image" src="https://github.com/user-attachments/assets/83db3cff-9f6c-4579-be32-bb00b3df7e0d" />

## Step 3
#### Run Commnad to Install Fleet Server

## Step 4
#### Connect to Fleet Server via Kibana UI






