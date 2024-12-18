How to configure new instance to send logs to Logstash?
-

## 1. Prerequisites:

- Remote Logstash Server: Running Logstash instance on a remote server, configured to receive logs on a **5044** port.
- **CentOS 9**: This tutorial only for specific OS.
- Root or sudo privileges: You'll need administrative privileges to install and configure software.
- Network Connectivity: Make sure the CentOS 9 server can reach the remote Logstash server on the configured port.

## 2. Add elastic repository

```bash
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
sudo tee /etc/yum.repos.d/elastic.repo <<EOF
[elasticsearch-8.x]
name=Elastic repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
failovermethod=priority
EOF
```







