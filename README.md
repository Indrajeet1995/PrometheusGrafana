Prometheus service Configurations

/etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=prometheus
Restart=on-failure

#Change this line if you download the 
#Prometheus on different path user
ExecStart=/home/prometheus/prometheus/prometheus \
  --config.file=/home/prometheus/prometheus/prometheus.yml \
  --storage.tsdb.path=/home/prometheus/prometheus/data

[Install]
WantedBy=multi-user.target






Node Exporter Service Configurations

/etc/systemd/system/node_exporter.service

[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/home/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target








Editing prometheus configuration file to add node_exporter and alerting rules file name

/etc/prometheus/prometheus.yml

rule_files:
  - "alert_rules.yml"


- job_name: 'node_exporter'
  static_configs:
    - targets: ['localhost:9100']







alert_rule.yml


groups:
 - name: example
   rules:
   - alert: InstanceDown
     expr: up == 0
     for: 1m

   - alert: HostOutOfDiskSpace
     expr: (node_filesystem_avail_bytes{mountpoint="/"}* 100) / node_filesystem_size_bytes{mountpoint="/"} < 99
     for: 1s
     labels:
       severity: warning
     annotations:
       summary: "Host out of disk space (instance {{ $labels.instance }})"
       description: "Disk is almost full (< xx% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"






Editing Alertmanager configuration file to add send mail using SMTP protocol in Prometheus

/etc/alertmanager/alertmanager.yml

route:
  group_by: [Alertname]
  # Send all notifications to me.
  receiver: email-me

receivers:
- name: email-me
  email_configs:
  - to: 
    from: 
    smarthost: smtp.gmail.com:587
    auth_username: "abc@gmail.com"
    auth_identity: "abc@gmail.com"
    auth_password: "abc"





Editing grafana.ini to configure SMTP in Grafana
/etc/grafana/grafana.ini

#################################### SMTP / Emailing ##########################
[smtp]
enabled = true
host = smtp.gmail.com:587
user = abc@gmail.com
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = abc
;cert_file =
;key_file =
skip_verify = true
from_address = abc@gmail.com
from_name = Grafana



##############################################################################

https://www.howtoforge.com/tutorial/how-to-install-prometheus-and-node-exporter-on-centos-7/ 
Reffered for installing and configuring Prometheus and node exporter

https://www.howtoforge.com/tutorial/how-to-install-grafana-on-linux-servers/
Reffered for installing and configuring Grafana

https://techexpert.tips/grafana/grafana-email-notification-setup/
Reffered for Grafana Notifications Setup

https://grafana.com/docs/grafana/latest/alerting/notifications/
Reffered for Grafana Notifications Setup

https://github.com/vipin-k/Prometheus-Tutorial/tree/master/Alert-Manager
Reffered for Alertmanager Setup

https://github.com/shazforiot/Prometheusalertmanager
Reffered for Alertmanager Setup

https://www.youtube.com/watch?v=U69YYWV7BW0&t=312s
How to get email alerts from grafana to gmail or your internal smtp server

https://www.youtube.com/watch?v=YUabB_7H710
Grafana Dashboard📊: Monitor CPU, Memory, Disk and Network Traffic Using Prometheus and Node Exporter

https://www.youtube.com/watch?v=a5wGy27cuBQ
Install Alert Manager & Configure Alert Manager in Prometheus