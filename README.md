# Active-Directory-Lab

## Objective
This project is going to go over how to set up an Active Directory environment with a Splunk instance that detects events that occur on AD and a Windows Machine. This project will go over how to set up Kali Linux so that you conduct brute force attacks to see what happens to AD. We will also be installing Atomic Red Team.

This project is going to be split up into five parts. The first part will consist of creating a logical diagram that outlines the relationships between each virtual machine. The second part will go over how to install the needed virtual machines, which include Windows Server, Windows 10, Kali Linux, and Splunk on an Ubuntu Server. We will be using ProxMox as the hypervisor for this project, as my current machine does not have sufficient specs to run multiple VMs, and my other laptop has more RAM. Part 3 will go over how to install and configure Sysmon and Splunk, which is going to be our SIEM. Part 4 will go over how to configure AD on a windows server and how to promote it to a domain controller. Part 5 will go over how to use Kali in an attack on the target machine, as well as how to download and use AtomicRedTeam to generate telemetry on the attacks. 

My current goal with this project is to familiarize myself with Active Directory, and how to set up an SIEM. I also want to learn how attacks can be conducted, and how security teams prevent them.

<img width="729" height="620" alt="image" src="https://github.com/user-attachments/assets/ee4fdc88-10ed-40b9-b6d9-fc6cef66ceab" />

## Skills Learned
- Active Directory configuration
- Red Team/ Blue Team
- Incident detection and response

## Steps
### Install VMs
The objective of this part is to install our virtual machines. We need a Windows 10, Kali Linux, Ubuntu Server, and Windows Server 2022. Fortunately, I have already installed a Windows 10 machine, Kali Linux, and a Windows Server, so all I need to do is download the ubuntu server for Splunk. However, I do think that I will have to do some reconfiguration so that everything is communicating properly.

Active Directory is a database that contains, users, groups, and computers. We will need to download Active Directory Domain Services so that we can elevate our AD server to a domain controller. We can do authentication and authorization using our AD domain. We can add objects like users, who each have their own attributes. 

The only thing that I had to do for this part was download the Ubuntu server where we will be configuring Splunk.

### Configure Splunk
This part will deal with configuring Splunk. The first step that I had to do was configure its network. In the tutorial that I am following, they used sudo nano to change the IP address to whatever they wanted it to be. Since I've already created a few networks, I skipped this step for now. I need to make sure to come back to this if I run into any networking problems. For now, I'm able to send out pings to the other VM's through the Splunk server. 

I managed to configure splunk onto the ubuntu server through some wget wizardry, but I did have to consult claude for that problem. Now, my main issue that I'm experiencing is that my target machine (Windows 10) is not experiencing any connectivity. I restored my Windows 10 VM to a back up and then I refreshed my Ethernet drivers, and that fixed the issue. 

Once I had connectivity, I downloaded splunk universal forwarder on the VM web browser. I configured the receiving indexer as the IP for the splunk server. Then, I downloaded sysmon, and then I downloaded the sysmon olaf config onto the machine. Then, I extracted the files and executed a command on PowerShell to execute the sysmon .exe file I downloaded using olaf's configuration. The command is .\Sysmon64.exe -i ..\sysmonconfig.xml. The -i indicates interactive and the .. indicates that I want to go back a directory. 

The next step that we must do is instruct the SplunkForwarder program what to send over onto the Splunk server. To do this, we must create a file called inputs.conf that has specific parameters for what to forward. You MUST not edit the inputs.conf file in the default directory. I placed the new inputs.conf file in the local directory. The specific instructions are as follows:
[WinEventLog://Application]

index = endpoint

disabled = false

[WinEventLog://Security]

index = endpoint

disabled = false

[WinEventLog://System]

index = endpoint

disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]

index = endpoint

disabled = false

renderXml = true

source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational


Then, we had to change some of the properties of SplunkForwarder in the services app. I changed it to a local system account and then restarted SplunkForwarder so that the changes can take effect. Once I restarted SplunkForwarder, I went to the web GUI and I was unable to receive any updates due to an error saying: 
- search:262 - reason="Search not executed: Dispatch Manager: The minimum free disk space (5000MB) reached for /opt/splunk/var/run/splunk/dispatch. user=admin.", concurrency_category="historical", concurrency_context="user_instance-wide", current_concurrency=0, concurrency_limit=5000.
I had to go to the settings and lower the minimum disk space to 50 MB. I restarted the GUI and I was still not seeing any changes, so I restarted the Splunk server itself. I then ran into some issues trying to get it started because I forgot to make Splunk boot up immediately once the Ubuntu server was booted. 

Then, I added an index called endpoint so that Splunk can detect our parameters from the inputs.conf file. After that, I configured receiving to listen to the port 9997, which is the same port used in a previous step. Now, there should be hits once you filter the search down to inputs=endpoints. 

### AD Configuration
First, I had to download an instance of Windows Server 2022. In this instance, I had to configure the AD domain and connect it to another instance of Windows 10, which will be the victim machine. I had some issues trying to get everything connected, but I managed to get them both connected by making both of them have the same DNS path. Once that was all set up, I created several organizational units, such as IT and Finance, and put some users in each department. 

### Conducting the Hack


