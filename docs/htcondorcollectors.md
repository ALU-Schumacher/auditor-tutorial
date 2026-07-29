## Install and configure HTCondor collector


### Install specific version for AUDITOR components
```
export AUDITOR_VERSION=0.10.1-1
dnf -y install auditor_htcondor_collector-${AUDITOR_VERSION}
```

## Configure AUDITOR HTCondor collector
An example config file and a unit file are shipped with the rpm installation adjust the config yaml file,
with /opt/auditor_htcondor_collector as its path for pre-0.10 versions:
Attention! replace: <REPLACE_WITH_HOSTNAME> with your fully qualified hostname (`hostname -f`)!
```
vim /etc/auditor/auditor_htcondor_collector.yml 
```
here I have used the example from the AUDITOR documentation
```
addr: localhost
port: 8000
timeout: 10
state_db: htcondor_history_state.db
record_prefix: htcondor
interval: 900 # 15 minutes
pool: <REPLACE_WITH_HOSTNAME>
log_level: INFO
schedd_names:
  - <REPLACE_WITH_HOSTNAME>
job_status: # See https://htcondor-wiki.cs.wisc.edu/index.cgi/wiki?p=MagicNumbers
  - 3 # Removed
  - 4 # Completed

meta:
  user:
    key: Owner
    matches: ^(.+)$
  group:
    key: "x509UserProxyVOName"
    matches: ^(.+)$
  submithost:
    key: "GlobalJobId"
    matches: ^(.*?)#  # As this regex contains a group, the value for 'submithost' is set to the matching group.

  # For `site` the first match is used.
  site:
    - name: "site_id_1"  # This entry
      key: "LastRemoteHost"
      matches: ^slot.+@site_id_1.+$
    - name: "site_id_2"  # This entry
      key: "LastRemoteHost"
      matches: ^slot.+@site_id_2.+$
    - name: "site_id_3"  # This entry
      key: "LastRemoteHost"
      matches: ^slot.+@site_id_3.+$
    - name: "UNDEF"  # If no match is found, site is set to "UNDEF"


components:
  - name: "Cores"
    key: "CpusProvisioned"
    scores:
      - name: "hepscore23"
        key: "MachineAttrHEPscore230"
  - name: "RequestedMemory"
    key: "RequestMemory"
  - name: "UsedMemory"
    key: "ResidentSetSize_RAW"
  - name: "CPUTime"
    key: "TotalCpuTime"
  - name: "DiskUsage"
    key: "DiskUsage_RAW"

tls_config:
  use_tls: False

```
Fix permissions on where the database will be created:
```
chmod 777 /opt/auditor_htcondor_collector
```
Start the htcondor collector service:
```
systemctl enable auditor_htcondor_collector.service 
systemctl start auditor_htcondor_collector.service 
```
Checking the status you should see:
```
 systemctl status auditor_htcondor_collector.service 
● auditor_htcondor_collector.service - HTCondor collector for AUDITOR
     Loaded: loaded (/etc/systemd/system/auditor_htcondor_collector.service; disabled; preset: disabled)
     Active: active (running) since Tue 2025-03-11 15:16:34 UTC; 2s ago
       Docs: https://alu-schumacher.github.io/AUDITOR/
   Main PID: 91396 (auditor-htcondo)
      Tasks: 1 (limit: 12148)
     Memory: 7.7M
        CPU: 85ms
     CGroup: /system.slice/auditor_htcondor_collector.service
             └─91396 //opt/auditor_htcondor_collector/venv/bin/python /opt/auditor_htcondor_collector/venv/bin/auditor-htcondor-collector --config /opt/auditor_htcondor_collector/auditor_htcondor_collector.yml

Mar 11 15:16:34 auditor-demo.novalocal systemd[1]: Started HTCondor collector for AUDITOR.
```
later on you will find the following errors:
```
auditor.collectors.htcondor - WARNING  - Could not find last job id for schedd 'schedd1.example.com' and recor>
auditor.collectors.htcondor - ERROR    - Error querying HTCondor history:
b'Unable to locate remote schedd (name=schedd1.example.com, pool=htcondor.example.com).\n'
auditor.collectors.htcondor - INFO     - Added 0 records.
```
This is expected: We have no HTCondor installed and running and therefore we cannot collect data atm. We see that in a later step.

