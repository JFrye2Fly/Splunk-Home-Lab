# Splunk-Home-Lab

## Introduction
In this project I enable logging on the Windows Event and PFSense Firewall logs, use Splunk Universal Forwarder to forward them to my Splunk instance. I ingest the logs finally after specifying the logs I want in the inputs.conf file. I then use Splunk to detect Brute Force and suspicious powershell scripts. Then I create alerts!

<img width="472" height="590" alt="image" src="https://github.com/user-attachments/assets/4194b315-dc45-427f-8fe7-326b550b1006" />

<br><br>
## Project Overview
<img width="855" height="912" alt="image" src="https://github.com/user-attachments/assets/ef0f4739-d750-4906-9c24-7539c3b7cac1" />

<br><br>
## Project Setup before the fun
- Launching a PFSense, Windows and Kali VM
- Enabling remote logging of DHCP, DNS and Firewall events on the PFSense firewall and sending it the Splunk instance on port 514

 <br> <img width="800" height="475" alt="image" src="https://github.com/user-attachments/assets/0cc95c0d-14ff-4fdb-b25c-e0e4ed02d7df" /><br>
 
- Creating a new index in Splunk called **network** on UDP Port 514... All the data from PFSense (firewall logs etc) will be send to this index in Splunk
  
<br> <img width="1714" height="553" alt="Creating Network Index and ingesting DHCP, DNS, Firewall logs" src="https://github.com/user-attachments/assets/995f3826-88a5-4774-84d3-4601f1e2cf56" /> <br>
 
- Installing Splunk Universal Forwarder on the Windows VM and forwarding the events to the main Splunk <img width="946" height="335" alt="Sending Application and Security events also" src="https://github.com/user-attachments/assets/ffa9dea0-0c1f-4b98-a676-4d76f559dc2d" />
stance




For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows] 
<img width="1440" alt="nsgattackmapbefore" src="https://github.com/user-attachments/assets/7a3ebdf5-d425-42ec-b1aa-41beb382dc92"> <br>

![Linux Syslog Auth Failures] <img width="1440" alt="linuxattackmapbefore" src="https://github.com/user-attachments/assets/3bc788b4-3f44-445b-b49e-d2d7d25b6b7a"><br>

![Windows RDP/SMB Auth Failures] <img width="1440" alt="windowsattackmapbefore" src="https://github.com/user-attachments/assets/82b3f951-8b1d-4f9a-b113-8dd414301fc1"><br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time Dec 5 2024 8:35 AM
Stop Time Dec 6 2024 8:35 AM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 25990
| Syslog                   | 21322
| SecurityAlert            | 0
| SecurityIncident         | 341
| AzureNetworkAnalytics_CL | 2714

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time Dec 8 2024 5:23 AM
Stop Time	Dec 9 2024 5:23 AM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 889
| Syslog                   | 4
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
