## Install AUDITOR Components from WLCG repo

Enable the WLCG repo and install the required components:
```
curl https://linuxsoft.cern.ch/wlcg/RPM-GPG-KEY-wlcg > /etc/pki/rpm-gpg/RPM-GPG-KEY-wlcg
yum-config-manager --add-repo https://linuxsoft.cern.ch/wlcg/wlcg-el9.repo
yum-config-manager --enable wlcg
```
