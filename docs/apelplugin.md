### Install and configure AUDITOR APEL plugin

### Install specific version for AUDITOR components
```
export AUDITOR_VERSION=0.10.1-1
dnf -y install auditor_apel_plugin-${AUDITOR_VERSION} 
```

### Configure AUDITOR APEL plugin
An example config file and a unit file are shipped with the rpm installation adjust the config yaml file, you might need to
adjust paths if you run an older version:
```
vim /etc/auditor/auditor_apel_plugin.yml
```
here I have used the example from the AUDITOR documentation
```
!Config
plugin:
  log_level: TRACE
  time_json_path: /opt/auditor_apel_plugin/time.json
  report_interval: 86400
  message_type: summaries

site:
  publish_since: 2024-01-01 06:00:00+00:00
  sites_to_report:
    SITE_A: ["site_id_1", "site_id_2"]
    SITE_B: ["site_id_3"]

messaging:
  host: msg.argo.grnet.gr
  port: 8443
  client_cert: /opt/auditor_apel_plugin/cert.crt
  client_key: /opt/auditor_apel_plugin/cert.key
  project: accounting
  topic: gLite-APEL
  timeout: 10
  retry: 3

auditor:
  ip: 127.0.0.1
  port: 8000
  timeout: 60
  site_meta_field: site
  use_tls: False

summary_fields:
  mandatory:
    NormalisedWallDuration: !NormalisedField
      score:
        name: hepscore23
        component_name: Cores
    CpuDuration: !ComponentField
      name: CPUTime
    NormalisedCpuDuration: !NormalisedField
      base_value: !ComponentField
        name: CPUTime
      score:
        name: hepscore23
        component_name: Cores
    VO: !MetaField
      name: group
    SubmitHost: !MetaField
      name: submithost
    Infrastructure: !ConstantField
      value: grid
    Processors: !ComponentField
      name: Cores
```
Normally you need to use a proper host certificate and key. For testing purposes here, we need to generate a key and certificate and place them where the APEL plugin is configured to look for them.
Change dir to `/opt/auditor_apel_plugin/` and execute the following commands:

Create a key:
```
openssl genrsa -out cert.key 2048
```
Create a certificate request:
```
openssl req -new -key cert.key -out cert.csr
```
Create a certificate:
```
openssl x509 -req -days 3650 -in cert.csr -signkey cert.key -out cert.crt
```


Start the APEL plugin service:
```
systemctl enable auditor_apel_plugin
systemctl start auditor_apel_plugin
```
Checking the status you should see:
```
systemctl status auditor_apel_plugin.service
● auditor_apel_plugin.service - APEL plugin for AUDITOR
     Loaded: loaded (/etc/systemd/system/auditor_apel_plugin.service; disabled; preset: disabled)
     Active: active (running) since Tue 2025-03-11 15:30:21 UTC; 3s ago
       Docs: https://alu-schumacher.github.io/AUDITOR/
   Main PID: 91729 (auditor-apel-pu)
      Tasks: 2 (limit: 12148)
     Memory: 27.0M
        CPU: 455ms
     CGroup: /system.slice/auditor_apel_plugin.service
             └─91729 //opt/auditor_apel_plugin/venv/bin/python /opt/auditor_apel_plugin/venv/bin/auditor-apel-publish --config /etc/auditor/auditor_apel_plugin.yml

Started APEL plugin for AUDITOR.
INFO     Enough time since last report, create new report (//opt/auditor_apel_plugin/venv/lib/python3.9/site-packages/au>
INFO     Getting records for site SITE_A with site_ids: ['site_id_1', 'site_id_2'] (//opt/auditor_apel_plugin/venv/lib/p>
INFO     No new records for SITE_A (//opt/auditor_apel_plugin/venv/lib/python3.9/site-packages/auditor_apel_plugin/publi>
INFO     Getting records for site SITE_B with site_ids: ['site_id_3'] (//opt/auditor_apel_plugin/venv/lib/python3.9/site>
INFO     No new records for SITE_B (//opt/auditor_apel_plugin/venv/lib/python3.9/site-packages/auditor_apel_plugin/publi>
INFO     Next report scheduled for 2025-03-12 15:30:22.034452 (//opt/auditor_apel_plugin/venv/lib/python3.9/site-package>
```
This is again expected, because we do not have any data and the sites `['site_id_1', 'site_id_2']` do not exist.


