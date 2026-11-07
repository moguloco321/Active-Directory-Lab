# Active-Directory-Lab

This project is going to go over how to set up an Active Directory environment with a Splunk instance that detects events that occur on AD and a Windows Machine. This project will go over how to set up Kali Linux so that you conduct brute force attacks to see what happens to AD. We will also be installing Atomic Red Team.

This project is going to be split up into five parts. The first part will consist of creating a logical diagram that outlines the relationships between each virtual machine. The second part will go over how to install the needed virtual machines, which include Windows Server, Windows 10, Kali Linux, and Splunk on an Ubuntu Server. We will be using ProxMox as the hypervisor for this project, as my current machine does not have sufficient specs to run multiple VMs, and my other laptop has more RAM. Part 3 will go over how to install and configure Sysmon and Splunk, which is going to be our SIEM. Part 4 will go over how to configure AD on a windows server and how to promote it to a domain controller. Part 5 will go over how to use Kali in an attack on the target machine, as well as how to download and use AtomicRedTeam to generate telemetry on the attacks. 

My current goal with this project is to familiarize myself with Active Directory, and how to set up an SIEM. I also want to learn how attacks can be conducted, and how security teams prevent them.

<img width="729" height="620" alt="image" src="https://github.com/user-attachments/assets/ee4fdc88-10ed-40b9-b6d9-fc6cef66ceab" />
