# Splunk-Home-Lab

## Introduction
In this project I enable logging on the Windows Event and PFSense Firewall logs, use Splunk Universal Forwarder to forward them to my Splunk instance. I ingest the logs finally after specifying the logs I want in the inputs.conf file. I then use Splunk to detect Brute Force and suspicious powershell scripts. Then I create alerts!

<img width="472" height="590" alt="image" src="https://github.com/user-attachments/assets/4194b315-dc45-427f-8fe7-326b550b1006" />

<br><hr><br>

## Project Overview
<img width="855" height="912" alt="image" src="https://github.com/user-attachments/assets/ef0f4739-d750-4906-9c24-7539c3b7cac1" />

<br><hr><br>

## Project Setup before the fun
- Launching a PFSense, Windows and Kali VM
- Enabling remote logging of DHCP, DNS and Firewall events on the PFSense firewall and sending it the Splunk instance on port 514

 <br> <img width="800" height="475" alt="image" src="https://github.com/user-attachments/assets/0cc95c0d-14ff-4fdb-b25c-e0e4ed02d7df" /><br>

 <br>
- Creating a new index in Splunk called **network** on UDP Port 514... All the data from PFSense (firewall logs etc) will be send to this index in Splunk
  
<br> <img width="1714" height="553" alt="Creating Network Index and ingesting DHCP, DNS, Firewall logs" src="https://github.com/user-attachments/assets/995f3826-88a5-4774-84d3-4601f1e2cf56" /> <br>

 <br>
- Installing Splunk Universal Forwarder on the Windows VM and forwarding the events to the main Splunk Instance

  <br> <img width="946" height="335" alt="Sending Application and Security events also" src="https://github.com/user-attachments/assets/ffa9dea0-0c1f-4b98-a676-4d76f559dc2d" /> <br>


<br><hr><br>

## Logs came in from the Windows VM and Splunk under the INDEX network
- **Windows Event VM Logs that came in on Splunk:** *Security, System, Application and Microsofot-Windows-Powershell/Operational*

<br>
  <img width="1890" height="976" alt="image" src="https://github.com/user-attachments/assets/db352b8a-b8f1-47de-b1ba-6b668d015972" />
<br>

<br><br>
- **PF Sense logs that came in on Splunk:** *Syslog (Firewall, DNS, DHCP events)
  <br><img width="1878" height="785" alt="image" src="https://github.com/user-attachments/assets/aba67fd6-afed-4888-9fe5-990513ca05cc" />
<br>

<br><hr><br>
## Detecting Brute Force Attack in Splunk

<h3>Windows Event Code 4625</h3>
 <p>We searched Windows Event Code 4625 and listed the account names and how many attempts each login failure attempt each account made </p>
 
 <br>
  <img width="1887" height="737" alt="Different users attemptin to brute force" src="https://github.com/user-attachments/assets/7ba89f9b-71ce-4b30-88a9-947db1188d1c" />
 <br>

 *Oh snap! Over 20 attempts to login to the Windows VM by 4 different users!*

 <br><hr><br>

 ### WAS THE BRUTE FORCE ATTACK SUCCESSFUL??? 
 <h4>Query used to search for many failures and one successful logon</h4> <br>
 
 <p>
  index=* source="WinEventLog:Security" EventCode IN (4624, 4625)
| where mvfind(Account_Name, "(?i)^splunk$") >= 0
| bin _time span=30m
| stats count(eval(EventCode=4625)) AS failures,
        count(eval(EventCode=4624)) AS successes,
        max(eval(if(EventCode=4624, _time, null()))) AS time_of_success
        BY _time, host, Source_Network_Address
| eval brute_force_confirmed=failures . " failures → 1 success"
| eval time_of_success=strftime(time_of_success, "%Y-%m-%d %H:%M:%S")
| eval window_start=strftime(_time, "%Y-%m-%d %H:%M:%S")
| fields window_start, host, Source_Network_Address brute_force_confirmed, time_of_success
| sort -window_start
 </p>

 <br>
   <img width="1563" height="352" alt="image" src="https://github.com/user-attachments/assets/f4cc6297-9ae7-4d77-af9d-5f1bcffb5764" />
 </br> 

 <h4>No external IP successfully Brute Forced but someone on the machine did!</h4>
 <h4> Maybe a stranger who found this computer successfully Brute Forced in... Still very dangerous!</h4>

<br>
 <img width="1879" height="870" alt="image" src="https://github.com/user-attachments/assets/c0cf2e55-1e6c-4782-9032-7ae0c118074a" />
<br>

 <h2>Brute Force Detection Alert Made</h2>
 <br>
 <p>I made an alert so that more 1 Brute Force success (at least 3 login failures and 1 success within a 5 min span) will trigger an alert, send and email and more! </p>

 <br>
  <img width="1373" height="361" alt="image" src="https://github.com/user-attachments/assets/46d1f34e-8612-4022-bf26-8bc0c4f0e25b" />
 <br>


### FINDING SUSPICIOUS POWERSHELL SCRIPTS IN SPLUNK
<h3>Did the malicious user use the ever popular powershell to run a malicious script</h3>

Searching the top event codes from the Powershell Logs show many 4103 and 4104 EventCodes...

<br>
<img width="1907" height="733" alt="Many 4104 and 4103 events" src="https://github.com/user-attachments/assets/6ec4be19-67a7-41fc-aeec-973c55cb746f" />
<br>

## 4104 = The actual contents of the Powershell Scripts that were run
<h3>I ran a search using the EventCode=4104 and I found something VERY INTERESTING</h3>

**A file named *magicpayloadpotion.txt* was ran!!!**
<img width="1883" height="937" alt="Magic Potion Payload" src="https://github.com/user-attachments/assets/5935853f-5571-4fe2-948b-e99864bb9f9e" />

### That's enough, lets search for .txt files... Those can often be suspicous

<br>
<img width="1894" height="1004" alt="WOW1" src="https://github.com/user-attachments/assets/854d3378-9fd8-4763-adc2-2fede51c7217" />
<br>

<br>
<img width="1307" height="897" alt="WOW2" src="https://github.com/user-attachments/assets/01295383-7892-451b-a6ec-b3881b1d009a" />
<br>

<br>
<img width="1919" height="739" alt="2026-04-07 01_08_56-Settings" src="https://github.com/user-attachments/assets/c503440f-e037-4269-92d9-c1cd1d8d0a5e" />
<br>

#WE'VE BEEN HIT!!! THOSE FILES ALMOST CERTAINTLY HAVE COMPROMISED US, LETS FIND SOME PROOF QUICKLY AND THEN ISOLATE THIS MACHINE!

## Conclusion

In this project I set up different VMS including a PFSense Firewall Instance, simulated attacks on my Windows VM and ingested those logs into the Splunk instance on my computer. I then verified that Splunk received those logs and use Splunk queries to find and display that data. 

The most important skills I learned in this project are:

#SplunkUniversal Forwarder
#Log Ingestion and reasons it may fail (inputs.conf folder is a must!)
#Configuring Splunk to receive logs
#Splunk Query Language specifics
#Patience
#Using DHCP server from PFSense to dynamically assign IPs to the VMs 
#Enabling logging on PFSense and sending them to a remote log server (remote log server was the splunk instance


## P.S. I still need to add the powershell malicious activity I found!



## Next Project
*In my Next Project I will have my Kali VM and Windows VM on different Networks. I will have the PFSense be on both networks and it will be the Gateway for my Kali Machine so that I can implement firewall rules and capture firewall logs!*
