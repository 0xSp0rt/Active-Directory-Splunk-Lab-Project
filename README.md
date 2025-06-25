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
Using Vultr cloud infrastructure provider, deploy 2 windows server 2022 virtual machines and 1 Ubuntu server machine. One of the Windows server 2022 will serve as a Domain controller named "SamADDS", the second windows server will serve as a TestMachine and the Ubuntu server will serve as an Indexer for Splunk and named "SamSPLK" <br/><br/>
  
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

Navigate to the firewall settings of each virtual machine and update to the created firewall group as shown below:  <br/><br/>

- Updating the firewall group of the virtual machine applies the inbound firewall rules created above to the deployed virtual machines. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507190/ss11_pgpxq5.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507191/ss13_himzet.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss19_vk2yay.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />

Navigate to the Settings of each virtual machine, Select IPv4 > Enable VPC:  <br/><br/>

- Enabling VPC assigns each virtual machine with a private ip address that allows for internal communication as shown in the last image below. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507192/ss15_yeesng.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749507190/ss12_ecxcaq.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss18_rd69lx.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss16_n9cvlj.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />

Assign static private ip addressing from the enabled VPC IP address to the windows server virtual machines:  <br/><br/>

- Go to Network connections > Internet Protocol Version 4. <br/><br/>
- Assign the VPC cloud IP address to its respective virtual machine using 10.22.96.4 for the Domain controller as shown below. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss20_tnminq.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss21_a9dq5t.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss22_feucg2.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Assign static private IP addressing from the enabled VPC IP address to the windows server test machine and using the private IP address of the domain controller as the DNS to enable the test machine to pull DNS records from the Domain controller virtual machine:  <br/><br/>

- Go to Network connections > Internet Protocol Version 4. <br/><br/>
- Assign the VPC cloud IP address to its respective virtual machine using 10.22.96.3 as the static IP address and 10.22.96.4 as the DNS server for the Test machine as shown below. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss24_lrmkxc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Log in to the Ubuntu server using ssh and ensure it is able to ping the Domain controller virtual machine:  <br/><br/>

- ssh root @public IP address. <br/><br/>
- ping private IP address of domain controller as shown below. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734959/ss17_z8a8nb.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss25_lsrm8o.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Log in to the Sam-ADDS virtual machine using Remote desktop protocol and installed Active directory roles and features as shown below:  <br/><br/>

- Go to server manager. <br/><br/>
- Add roles and features. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss26_nqzrlx.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss27_ccapqq.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734960/ss28_cfbiio.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
After installing the Active directory roles and features, I proceeded to promote the Virtual machine to domain controller and created my domain:  <br/><br/>

- Go to server manager > promote to domain controller. <br/><br/>
- Add a New forest. <br/><br/>
- Create a Password. <br/><br/>
- Install and restart after installation. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749734961/ss29_k4htkd.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739404/ss31_uwmbnd.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739404/ss32_nf550y.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739405/ss33_i0ihbf.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739405/ss34_rbweeb.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />

After restarting, I proceeded to create a new user in Active directory as shown below:  <br/><br/>

- Go to server manager > Tools > Active directory users and computers. <br/><br/>
- Users > New > User. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739406/ss36_zqmams.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739408/ss37_qz1hvk.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739408/ss38_yyy6fz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739408/ss39_emw1w3.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Log in to the Sam-TestMachine VM to rename the VM and add the server to the Domain:  <br/>

- Go to This PC > Properties > Active directory users and computers. <br/><br/>
- Rename this PC > Domain > Enter domain name. <br/><br/>
- Enter Administrative username and password > Restart PC. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739409/ss40_m2cgvs.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739410/ss42_icdsuc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739411/ss43_caz6mm.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739411/ss44_bmmio2.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Log in to the Sam-TestMachine VM with the newly created user account to enable remote desktop access. Enabling remote access allows us to remotely connect to the newly created user account using RDP:  <br/>

- Sign in with newly created user. <br/><br/>
- Go to Remote desktop settings > Show settings. <br/><br/>
- Enter Administrative username and password. <br/><br/>
- Allow enable remote desktop connection > Select Users > Ok. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739412/ss46_tuu9xr.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739413/ss48_bngi1v.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739414/ss49_cj4p2n.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1749739414/ss50_myw7ee.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111821/ss51_i1zkuf.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
The next step would be to install splunk on the Ubuntu virtual machine to serve as the index:  <br/>

- Using command prompt on local host, ssh into the ubuntu virtual machine. <br/><br/>
- Run "sudo apt-get update && apt-get upgrade" to update the directories. <br/><br/>
- run the command "wget -O with splunk indexer for ubuntu download link as shown in the image below" to download splunk. <br/><br/>
- After download, change directory to the splunk folder on the ubuntu machine. <br/><br/>
- Start splunk on the ubuntu machine using the command "./splunk start" . <br/><br/>
- Accept the license agreement as shown below, create a username and password, and you will be provided with the splunk web interface url. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111825/ss53_olkhwm.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111844/ss55_zitgtf.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111846/ss56_cx3vrc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111848/ss57_a7ejxq.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111849/ss58_nwubk3.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111851/ss59_jo0rjy.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111855/ss60_nhpja9.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111853/ss60.5_udguix.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
To gain access to the splunk web interface on the local host, it is important to allow tcp on port 8000 on the ubuntu server and Vultr inbound firewall group rules as shown below:  <br/>

- Go to the Vultr interface > Select the Ubuntu machine > Settings > Firewall. <br/><br/>
- Go to inbound rules, allow TCP traffic for port 8000 and select "My IP" as source. <br/><br/>
- SSH into the Ubuntu machine using command prompt and run the command "ufw allow 8000". <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111856/ss61_ek44pp.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750113935/ss62_vtkzbw.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111860/ss63_djcb5p.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
Gain access to the splunk web interface with the url provided and enter the created username and password during splunk installation to configure splunk and install required applications in Splunk:  <br/>
**Note**: The reason for installing Splunk add on for Microsoft Windows is to allow us to be able to detect logged on or created users on the Windows machines. <br/>

- Go to the splunk web interface > log in with username and password. <br/><br/>
- Once logged in to the splunk dashboard, Go to Administrator > select Preferences > Change timezone to your preferred Timezone and save. <br/><br/>
- Go to Dashboard, select Apps > Find more Apps > Search Splunk add on for Microsoft windows and install as shown below. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111862/ss64_as0rls.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111864/ss65_e7ed6n.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111866/ss66_ie2zg9.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111869/ss67_r2hdz9.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111870/ss68_ac2rzp.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111872/ss69_cio2ti.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750111873/ss70_tdrpsg.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
</Details>
<Details>
<summary><h3>Splunk Set-up</h3></summary>
To set up Splunk, it is important to create Indexes for required logs to be aggregated to. The setup also includes setting up forwarding ports as well as installation and configuration of splunk forwarders on required endpoints to forward logs to the indexer. <br/>
  
- Go to Settings in Splunk > Add data > Indexes. <br/><br/>
- Save Indexes. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867671/ss71_iycjyc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867671/ss72_cuhuuc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
To configure forwarding and receiving ports. <br/>

- Go to Settings in Splunk > Add data > Forwarding and receiving. <br/><br/>
- Configure Receiving > New Receiving Port. <br/><br/>
- Listen on Port 9997 > Save. <br/><br/>
- Go to ubuntu server > run the command "ufw allow 9997"
The above command allows for port 9997 to be open on the ubuntu server to allow for the server to listen on port 9997 for the forwarders.

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867671/ss73_qjuhac.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867672/ss74_luzzbe.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867674/ss75_thnlv7.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867675/ss76_otdmnv.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867675/ss77_jhoshp.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750867677/ss78_ulq9xr.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
To set up Splunk forwarders, Download and install the splunk dedicated forwarders on every virtual machine that needs to forward its logs to the indexer: <br />

- Start Universal forwarder setup > select on premise setup for on premise splunk set up > Next. <br/><br/>
- Select Username > Next. <br/><br/>
- Input Private IP address of Indexer and Port 9997 for indexer > Next. <br/><br/>
- Install > Enter administrative password. <br/><br/>
The above steps will be repeated for the Domain controller windows server as well to enable forwarding of logs to the indexer.

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869003/ss80_qtujwy.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869006/ss81_cg3mjl.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869007/ss82_q0r937.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869008/ss83_ejomdc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869009/ss84_clbme8.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869010/ss85_yzjou1.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869011/ss86_tan6kr.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
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
