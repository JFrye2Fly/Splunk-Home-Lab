# Splunk-Home-Lab

## Introduction
In this project I enable logging on the Windows Event and PFSense Firewall logs, use Splunk Universal Forwarder to forward them to my Splunk instance. I ingest the logs finally after specifying the logs I want in the inputs.conf file. I then use Splunk to detect Brute Force and suspicious powershell scripts. Then I create alerts!

<img width="472" height="590" alt="image" src="https://github.com/user-attachments/assets/4194b315-dc45-427f-8fe7-326b550b1006" />

<hr> 

## Project Overview
<img width="855" height="912" alt="image" src="https://github.com/user-attachments/assets/ef0f4739-d750-4906-9c24-7539c3b7cac1" />

<hr>

## Project Setup before the fun
- Launching a PFSense, Windows and Kali VM
- Enabling remote logging of DHCP, DNS and Firewall events on the PFSense firewall and sending it the Splunk instance on port 514

 <br> <img width="800" height="475" alt="image" src="https://github.com/user-attachments/assets/0cc95c0d-14ff-4fdb-b25c-e0e4ed02d7df" /><br>
 
- Creating a new index in Splunk called **network** on UDP Port 514... All the data from PFSense (firewall logs etc) will be send to this index in Splunk
  
<br> <img width="1714" height="553" alt="Creating Network Index and ingesting DHCP, DNS, Firewall logs" src="https://github.com/user-attachments/assets/995f3826-88a5-4774-84d3-4601f1e2cf56" /> <br>

 
- Installing Splunk Universal Forwarder on the Windows VM and forwarding the events to the main Splunk Instance

  <br> <img width="946" height="335" alt="Sending Application and Security events also" src="https://github.com/user-attachments/assets/ffa9dea0-0c1f-4b98-a676-4d76f559dc2d" /> <br>


<br><hr><br>

## Logs came in from the Windows VM and Splunk under the INDEX network
- **Windows Event VM Logs that came in on Splunk:** *Security, System, Application and Microsofot-Winds-Powershell/Operational*

<br>
  <img width="1890" height="976" alt="image" src="https://github.com/user-attachments/assets/db352b8a-b8f1-47de-b1ba-6b668d015972" />
<br>

- **PF Sesne logs that came in on Splunk:** *Syslog (Firewall, DNS, DHCP events)

<br>
  <img width="1878" height="785" alt="image" src="https://github.com/user-attachments/assets/aba67fd6-afed-4888-9fe5-990513ca05cc" />
<br>



## Conclusion

In this project, a mini 
