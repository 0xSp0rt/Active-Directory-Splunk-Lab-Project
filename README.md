# Active Directory Splunk-lab-project
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745530316/qualys-628x353_a5j85o.avif" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<h1>Active directory splunk lab log project</h1>

<h2>Description</h2>
This project aims to provide a walkthrough on configuring Active directory on Windows Servers 2022 vm, configuring domain controllers and joining the domain, installation of splunk forwarder on the Windows server virtual machines as well as installation of splunk enterprise indexer on the Ubuntu Virtual machine. The aim of the installation of the indexer and forwarders is to collect and aggregate logs from various endpoints to detect and triage events and incidents on these devices. In this project, we will be finetuning and using splunk queries to detect specific events in the virtual machines, using splunk queries to map IP addresses to their geolocation, generating alerts in splunk to detect these events as well as automation through the use of playbooks using shuffler and slack to improve MTTD and MTTR.  <br/>
The aim of this project is to simulate a real SOC environment through the generation of Alerts by leaving RDP port open and using Splunk enterprise for the detection bruteforce and login attempts from malicious IP addresses on the internet.
<br />


<h2>Utilities Used</h2>

- <b>Windows Server 2022 VMs</b>
- <b>Ubuntu server VM</b>
- <b>Remote desktop protocol</b>
- <b>Splunk Enterprise Edition</b>
- <b>Shuffle Automation</b>
- <b>Slack</b>



<Details>
  <summary><h2>Project walk-through:</h2></summary>

<p align="left">
Using Vultr cloud infrastructure provider, deploy 2 windows server 2022 virtual machines and 1 Ubuntu server machine. <br/><br/>
  
- <b>Step 1:</b> Deploy 3 virtual machines in the same location as shown below   <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507186/ss4_jq9enz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Create a firewall group as well as firewall rules to secure the deployed virtual machines:  <br/><br/>

- <b>Step 1:</b> Navigate to Network, click on add firewall group.   <br/><br/>
- <b>Step 2:</b> Create a firewall group name and add firewall group.   <br/><br/>
- <b>Step 3:</b> Update inbound firewall rules to allow SSH and RDP connections from your ip address as shown below.   <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507186/ss5_ne5qtb.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507188/ss6_gstfro.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507188/ss7_pu9fhc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Start the virtual machine created with the virtual scanner vhdx file in Hyper-V and input the personalization code provided by Qualys for the scanner in the field highlighted below: <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745552377/3_b47k9j.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Once personalization key has been entered, wait for the personalization process to be complete. LAN IP is displayed after personalization is completed as shown below:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745552375/4_b1zy9v.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
After Personalization is complete, go back to the Qualys web console and click on check activation as shown below to verify Virtual scanner activation. Once activation is confirmed, click done as shown below:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745553201/5_qquzwv.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
To confirm virtual scanner has been connected to the web console, go to scans > appliances as shown in the images below and the virtual scanner should be displayed in the web console. We can then proceed to Assets as highlighted below:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745553199/6_ijhlx5.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Go to assets, select Add IPs for scanning > New > IP Tracked addresses as shown below:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745553197/7_pchzvt.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Go to Subscription IPs to include IPv4 address of the virtual machines to be scanned and click add as highlighted below:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745554600/8_wtdp7o.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Go to Scans > Option Profiles to create Option profiles for the type of scan to be conducted Qualys as shown below then click save. In this case, we will be conducting an Uncredentialed scan which only scans open ports to detect vulnerabilities:  <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745554598/9_bjyphu.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745554595/10_ljw1tv.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
To begin scan, Go to scan on the web console > New > Scan > Give the scan a Title, choose an Option profile for the type of scan to be conducted, pick the virtual scanner for the scan, include IP addresses of the devices to be scanned and click Launch as shown below to begin the scan. <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745554593/11_oeya4i.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
PS: Before the scan begins, it is important to disable the Windows defender firewall on the Windows device as shown below to allow for the scan to be conducted. The scan will be queued for a couple of minutes before it begins:   <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745556225/12_zp8rgs.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Attached below is a result of the Uncredentialed scan detecting vulnerabilities in the Windows 10 VM on Hyper-V with a summary of 24 vulnerabilities discovered with a 3.0 severity level. Qualys allows for these results to be exported as PDF or XML for further analysis and documentation. <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745556223/13_fw7tfb.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
</Details>
<Details>
<summary><h3>Credentialed Scan</h3></summary>
To conduct credentialed scans which allows for an indepth vulnerability scan of the virtual machine, It is important to make the following configurations with Services, Managed Advanced sharing settings, User account control settings. <br/>
Go to my Services > Remote Registry properties > Change Startup Type to Automatic > Apply changes > Start the Service:   <br/>
This allows for users to make changes to windows registry settings. <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597866/14_quldzn.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597864/15_odeiuu.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Right-click Server > Properties > Change Startup Type to Automatic > Apply Changes > Start Server. <br/>
This allows file, print and sharing services over the network. <br/>

Go to Managed Advanced sharing settings > Turn on File sharing and Network discovery > Save Changes. <br/>

Go to User account control settings and apply the changes as shown below. <br/>
This grants Qualys necessary permission to conduct an indepth scan. <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597863/16_bxwaph.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597860/17_e3hbwv.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597858/18_ku0bi1.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597856/19_zbadkv.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Go to Windows registry directory highlighted below. Right-click and create a new 32 bit value > Create the Value name to LocalAccountTokenFilterPolicy > Change Value data to 1 as shown below then proceed back to the Qualys web console and go to Authentication as shown below: <br />
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745603992/ss37_krjebp.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745604327/ss38_b68gub.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745605936/21_muqmix.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Under Authentication > New > Operating systems (Select the required operating system of the devices which is Windows in this case) > Select record title and name it > Login Credentials and input the login credentials of the devices to be scanned as shown in the screenshots below > IPs (Input IPs of the device to be scanned) > Save:   <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745597851/22_se44oe.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745606407/23_bhvxrr.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/> 
<br />
<br />
Go to Option profile > Select the previously created option profile as shown in the images below > Select Edit > Scroll to Authentication > Select Windows > leave the rest of the settings on default > Save > Go to Scans:  <br />
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745606403/24_ioquzt.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745606399/25_eopvmq.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
Click New > Create Title > Fill as shown below > Include IP addresses (Of the devices that needs to be scanned) > Launch Scan. The result of the scan below indicates and shows a total of 382 confirmed vulnerabilities with an indepth look at the scan results showing vulnerabilities from Applications like Mozilla firefox and VLC media player as shown in the images below. <br />
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745606395/26_drxqw5.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745606391/27_lrrmsh.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
<h3>Vulnerability Remediation</h3>
An indepth analysis of the credentialed vulnerability scan results indicates that a lot of the vulnerabilities discovered had a severity level of 4 and 5, indicating their risk level and exploitability. Most of these vulnerabilities were from old versions of Mozilla firefox and VLC media player. To fix these vulnerabilities, we can either update these Applications to patch these vulnerabilities or uninstall these old versions to make the virtual machine more secure. <br />
To patch these vulnerabilities and make the virtual machine more secure, we would be uninstalling the Mozilla firefox program as well as the VLC media player as shown in the images below to patch the vulnerabilities attached to those programs: <br />
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878474/ss53_mkme16.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878479/ss55_y6wpoq.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878482/ss56_yrsbja.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878488/ss58_js92ki.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878491/ss59_urolxx.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
After Uninstalling these programs as shown in the images above, The next step would be to relaunch the scan as shown below, include the IP address of the virtual machine to be scanned and await the results. <br />
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878494/ss60_etj2su.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878496/ss61_ifmjhj.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
<h3>Post Remediation Scan Results</h3>
The result of the scan after remediating the mozilla firefox and vlc media player vulnerabilities showed the following results. The scan results after remediation showed a reduction in the number of severe vulnerabilities compared to the results of the scan before remediation which is due to the removal of the firefox and vlc media player programs causing some of the vulnerabilities. The result of the post remediation scan shows 228 vulnerabilities detected compared to an initial 338 vulnerabilities detected before remediation. <br/ >
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1745878499/ss62_hczytw.png" height="80%" width="80%" alt="Vulnerability assessment on windows 10 host using Qualys"/>
<br />
<br />
</Details>
<h3>Summary</h3>
The results of the multiple scans conducted as shown in the walkthrough above goes to show the importance of conducting a credentialed scan over a non credentialed scan as it gives Qualys the capability to perform an indepth scan to detect various CVEs that might exist on the host. The Post remediation scan results also show the effectiveness of Qualys in detecting patches that have been made after the inital scan. <br />
Although this vulnerability management was conducted on a Hyper-v virtual machine in a smaller Home lab environment, It has a use case for enterprise environments where organizations have multiple virtual machines that needs to be scanned for vulnerabilities that might exist in the virtual environment before conducting patch management on these machines. It is advisable that vulnerability and patch management be conducted on a regular basis to detect new CVEs and keep the host machines as secure as possible.

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
