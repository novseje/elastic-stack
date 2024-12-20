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

## 3. Configure Filebeat

### 3.1. Install Filebeat

```bash
sudo dnf install filebeat
```

### 3.2. Enable and configure data collection modules

```bash
sudo filebeat modules enable system
```

Enable filesets in the module

```bash
sudo vi /etc/filebeat/modules.d/system.yml
```

Change `enabled: false` to `enabled: true` for both filesets:

```
- module: system
  syslog:
    enabled: true
  auth:
    enabled: true
```

### 3.3. Configure Filebeat:

Edit filebeat.yml:

```bash
sudo vi /etc/filebeat/filebeat.yml
```

In `filebeat.inputs` section, below `type: filestream` add `type: log` block.

```
filebeat.inputs:
- type: filestream
  # other lines ...

- type: log
  enabled: true
  paths:
    - /root/.pm2/logs/*.log
  json.keys_under_root: true
  json.message_key: time 
```

In `paths` specify the log files you want to monitor.
For JSON logs, ensure the `json.keys_under_root` is set to true.
If your json log has a message key field set `json.message_key`.

Also, you can define strict paths:
```
    - /root/.pm2/logs/index-error.log
    - /root/.pm2/logs/index-out.log
    - /root/.pm2/logs/scanner-index-out.log
```

Configure Logstash Output.

Uncomment `output.logstash` section and set specify the hostname and port of your remote Logstash server.: 

```
output.logstash:
  hosts: ["111.111.222.222:5044"]
```

And comment `output.elasticsearch`

```
#output.elasticsearch:
#  hosts: ["localhost:9200"]
```

**Important!** `output.elasticsearch` and `output.logstash` cannot be active at the same time! You cannot leave `output.elasticsearch` uncommented.

#### 3.4. Start and Enable Filebeat service:

```
sudo systemctl start filebeat
sudo systemctl enable filebeat
```






