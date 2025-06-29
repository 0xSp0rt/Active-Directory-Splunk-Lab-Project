# Active Directory Splunk-lab-project
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750972688/SOC_project.drawio_kkb8v1.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
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
  <summary><h2>Virtual Machine Set-up walk-through:</h2></summary>

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
- Go to Splunk forwarder default folder highlighted below > Copy iputs.conf file > Paste in Splunk system local folder. <br/><br/>
- Open the inputs.conf file in local folder using Notepad > Ensure the WinEventlog highlighted below is false > Save. <br/><br/>
- Go to Services > Splunk forwarder properties > log on > Local system account select > Apply. <br/><br/>
- Restart Splunk forwarder properties. <br/><br/>
The above steps will be repeated for the Domain controller windows server as well to enable forwarding of logs to the indexer.

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869003/ss80_qtujwy.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869006/ss81_cg3mjl.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869007/ss82_q0r937.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869008/ss83_ejomdc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869009/ss84_clbme8.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869010/ss85_yzjou1.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869011/ss86_tan6kr.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869013/ss87_xmykaf.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869014/ss88_le9b8p.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869015/ss89_sybbvc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750869016/ss90_wcbob4.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750870574/ss91_xqkqcf.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750870575/ss92_wxafym.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750870577/ss93_jk66ns.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750870578/ss94_zeh3sr.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
To generate Telemetry in Splunk, I proceeded to turn on RDP on the firewall and searched for unauthorized failed login attempts in Splunk: <br />

- Go to the vultr firewall group and turn on RDP for all traffic. <br/><br/>
- Go to splunk > Enter the Query highlighted below to detect failed login attempt with eventcode 4625. <br/><br/>
- Go to Save > Save as ALert. <br/><br/>
  
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872076/ss101.5fw_nlcntz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872077/ss102_nunbpg.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872079/ss103_izi1lp.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
After generating telemetry, I proceeded to create a dashboard to map failed unauthorized logins from malicious ip addresses to their geolocation: <br />

- Go to Dashboard > Create New Dashboard. <br/><br/>
- Create Dashboard title > Classic dashboards > Create. <br/><br/>
- Add Panel > New > Chloropleth map > Enter highlighted search string to map ip to geolocation by country > Add to Dashboard. <br/><br/>
**Note**: Using this search query and creating this dashboard allows us to be able map the malicious ip addresses to their geolocation by country. <br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872802/ss112_ewegle.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872826/ss113_cmsmbo.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872827/ss114_tchlh0.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872829/ss115_hrfzxt.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750872830/ss116_d6nlmk.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>  
<br />
<br />
</Details>
<Details>
<summary><h3>Shuffler Automation</h3></summary>
To improve MTTD and MTTR, It is important to incorporate Automation into this setup. This workflow aims to parse alerts from Splunk with a webhook and send those alerts as notifications into a dedicated slack channel and an email notification to disable the compromised user account in active directory. <br />

- Go to Shuffler > Workflow > Create Workflow > Name workflow. <br/><br/>
- Grab webhook > Create Webhook Name > Copy Webhook Link. <br/><br/>
- Go to Splunk saved alerts > edit > select Webhook > Paste webhook link > Save. <br/><br/>
- Go to shuffler workflow > Drag slack into the workflow > Authenticate into your created Slack account. <br/><br/>

<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874363/ss117_feynq1.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874365/ss118_ct24qs.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874367/ss119_gaoop6.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874368/ss120_e5ujix.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874370/ss121_hpi3bc.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874371/ss122_t4gd2o.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874373/ss123_acs3fd.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
After Authenticating Slack, we proceed to create a Slack channel and configure Shuffler to send in the alerts to the created Slack channel. <br />

- Go to Slack > Create Channel > Blank Channel > Channel name > Create Channel. <br/><br/>
- Go to shuffler > select Slack > Select Find actions > Post a message. <br/><br/>
- Under slack > Select Runtime arguement (to configure info to be sent through alerts). <br/><br/>
- Go to slack > copy Channel ID > Post channel ID under Slack in Shuffler > Save. <br/><br/>
- Run the workflow. **You should get a slack notification of your configured alert**. <br/><br/>
- Go to Slack > Select Action Trigger > Select desired information to be sent using Runtime Parameters > Select mode of action(email or sms) > Enter desired Email address. <br/><br/>
- Run the workflow and The email with the action trigger should be received. <br/><br/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874378/ss125_k3ds9l.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874380/ss126_nc8ont.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874382/ss127_db8oly.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874384/ss128_kdq7pu.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874386/ss129_u4mxgz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750874389/ss130_cvdhdd.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967359/ss131_n5aqho.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967359/ss132_r8qwsv.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967359/ss133_wdz6oz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967360/ss134_bnv9d2.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967360/ss135_vwvls0.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750969236/ss136_pxmoej.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750969236/ss138_vzrboz.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967360/ss139_dfoj3a.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<img src="https://res.cloudinary.com/dbglnqdha/image/upload/v1750967361/ss140_bkpjko.png" height="80%" width="80%" alt="Active Directory Splunk-lab-project"/>
<br />
<br />
</Details>
<h3>Summary</h3>
The result of this Splunk project allows us to be able to detect Alerts and improve response Time as well as MTTD by incorporating Automation, the use of alerts also saves man hours by avoiding the need for security operations center analysts to constantly search for alerts and be notified as soon as they occur in their environments. <br />

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
