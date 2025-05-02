#Splunk SIEM and Forwarder Installation Guide
This guide details the steps for installing Splunk Enterprise Server (SIEM) and the Splunk Universal Forwarder on Linux systems. This setup is commonly used to collect logs from various sources (like honeypots) and push them in Splunk for analysis and monitoring.
##Step 1: Install Splunk Enterprise Server (SIEM)
These steps are for installing the main Splunk Server instance, which will receive and process logs from the forwarders.
Download Splunk Server .deb Package:
Use wget to download the Splunk Enterprise server package. Replace the URL if you need a different version or architecture.
```wget -O splunk-9.3.2-d8bb32809498-linux-2.6-amd64.deb https://download.splunk.com/products/splunk/releases/9.3.2/linux/splunk-9.3.2-d8bb32809498-linux-2.6-amd64.deb```
