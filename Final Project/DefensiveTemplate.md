# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

The following machines were identified on the network:
- ELK 
  - **Operating System**: Ubuntu 18.04.1 LTS
  - **Purpose**: Server used to log data to Kibana Dashboards
  - **IP Address**: 192.168.1.100
- Kali
  - **Operating System**: Kali
  - **Purpose**: Linux machine used for penetration testing
  - **IP Address**: 192.168.1.90
- Target 1 
  - **Operating System**: Debian Linux 8
  - **Purpose**: Hosts vulnerable WordPress server
  - **IP Address**: 192.168.1.110
- ELK 
  - **Operating System**: Debian Linux 8
  - **Purpose**: WordPress server host with harder security
  - **IP Address**: 192.168.1.115

### Description of Targets


The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors


Alert 1 is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 'http.response.status_code'
  - **Threshold**: above 400 in 5 minutes
  - **Vulnerability Mitigated**: Enumeration, Brute Force
  - **Reliability**: This alert is highly reliable and does not generate a lot of false results. Wpscan attempts were detected as a high number of HTTP 400 error codes were created.

#### HTTP Request Size Monitor
Alert 2 is implemented as follows:
  - **Metric**: WHEN sum() of http.request.bytes OVER all documents
  - **Threshold**: above 3500 in 1 minute
  - **Vulnerability Mitigated**: DoS attack, Remote Code Injection, or any high-traffic created from an event
  - **Reliability**: This alert is a medium-rated reliability. As false positives can be triggered by non malicious activity or legitimate high volume.

#### CPU Usage Monitor
Alert 3 is implemented as follows:
  - **Metric**: WHEN max() OF system.process.cpu.total.pct OVER all documents
  - **Threshold**: above 0.5 for the last 5 minutes
  - **Vulnerability Mitigated**: malware, malicious software
  - **Reliability**: This alert is also a medium-rated reliability. Although it can correctly depict suspicious activity that is resourse-intensive, it can provide false negatives if malicious scripts use lower CPU resources.
