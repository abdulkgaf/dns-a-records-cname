<p align="center">
  <img src="https://github.com/user-attachments/assets/b16601e3-75cc-4e5c-9e85-f95de8baf8eb" alt="![image]"
    </p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual MAchines</h1>
In this tutorial, We will be operating on Microsoft Azure and observing various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, HTTPS/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Sysyems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 22.04

<h2>High-Level Steps </h2>

- Creating Resource Groups and Virtual Machines in Azure
- Observe ICMP Traffic
- Configuring a Firewall (Network Security Group)
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic


<h2>Actions and Observations</h2>

1. Creating Resource Groups and Virtual Machines

 Resource Group:
- To create a Resource Group, go to the Azure Portal. In the middle of the homepage under 'Azure Services', click on 'Resource groups' and then select 'Create' to begin the process'.

![Screenshot 2025-05-02 161517](https://github.com/user-attachments/assets/b71ae5ea-cbf2-43b0-8f7d-69a8376dbcff)

- All resources in a subscription are billed together so leave that as default.
- Resource group name can be named to your desire. I will call mine "RG-NSGs-Lab".
- When selecting a resource group region, it's recommended that you select a location close to where your control operations originate.

![Screenshot 2025-05-02 2211482](https://github.com/user-attachments/assets/57310bb4-d7c1-4de3-aa04-9f5bded6e110)

- Click 'Review + create'.

![Screenshot 2025-05-02 223356](https://github.com/user-attachments/assets/cbeb6f49-381c-4e54-b569-a56d61447b0c)

- Then click 'Create'.

![Screenshot 2025-05-02 223508](https://github.com/user-attachments/assets/c13dbdac-728f-496f-ae81-3db33248b357)

 Virtual Machines:
- In the Azure Portal, use the search bar at the top to search for 'Virtual Machines" and select it.
- You are required to create two virtual machines.
- In the display area, click 'Create' -> 'Azure virtual machine'.

![Screenshot 2025-05-02 225357](https://github.com/user-attachments/assets/6af18902-841e-4409-8c26-51c1576faed9)

- Under 'Project details', assign your new resource group you just created (RG-NSGs-Lab).
- Give your Virtual Machine name "windows-vm".
- Select the resource region the same as the Resource groups ((Asia Pacific) Australia East).

![Screenshot 2025-05-03 002830](https://github.com/user-attachments/assets/7a998ae6-1294-44f3-8505-ec2ea37b34e0)

- For Image, select 'Windows 10 Pro version 22H2 - x64 Gen2'.
- Recommended for 'Size' you use "Standard_D2s_v5 - 2 vcpus, 8 GiB memory".
- For 'Administrator Account' create your Username "labuser" and password can be anything. e.g; "Cyberlab123!".

![Screenshot 2025-05-03 004155](https://github.com/user-attachments/assets/fec762f8-fe8c-4778-bbaa-0e5362ef3f47)

- Be sure to tick the box for eligibility of a windows licence.
- Click 'Next : Disks' -> 'Next : Networking'.

![image](https://github.com/user-attachments/assets/abf9bcbe-64bf-4902-a8a7-bacf3eaa65b1)

- Change the 'Virtual Network' name. Click on 'Create new'.
- Change it to "Lab2-Vnet" the click 'ok'.

![image](https://github.com/user-attachments/assets/63a21391-5646-4f3d-bccd-48ca4bdc3b8d)

- Click 'Review + Create'.

![image](https://github.com/user-attachments/assets/7a82ac39-8f99-4fe9-bc17-03c854742699)

- Wait for the VM to process till you see "Validation passed" on the top of the page.

![Screenshot 2025-05-06 141709](https://github.com/user-attachments/assets/0bd90452-ceca-46f3-b2e3-6e792e9a564c)

- Then click 'Create'.

![Screenshot 2025-05-06 141743](https://github.com/user-attachments/assets/86ed7e8f-ccb0-423f-b337-969517eaebf1)

- You have successfully created a Windows Virtual Machine.
- Click on 'Create another VM' for Linux (Ubuntu) VM.

![image](https://github.com/user-attachments/assets/c3b0a520-5df7-4c29-a246-0c5643b25da4)

- Under 'Project details', assign your new resource group you just created (RG-NSGs-Lab).
- Give your Virtual Machine name "linux-vm".

![image](https://github.com/user-attachments/assets/6666c8b8-0b5a-450a-a01b-3a0751da96ef)

- For Image, select 'Ubuntu Server 22.04 LTS - x64 Gen2'.
- Recommended for 'Size' you utilise "Standard_D2s_v5 - 2 vcpus, 8 GiB memory".

![image](https://github.com/user-attachments/assets/7e9e4fb0-7bc3-44b7-9387-82df0beb6e48)

- Under Administrator account for 'Authentication type' click 'Password'.
- Create your Username "labuser" and password can be anything. e.g; "Cyberlab123!".

![Screenshot 2025-05-03 004155](https://github.com/user-attachments/assets/1c567476-7fb9-476f-97ee-6b0a3986416c)


- Click 'Next : Disks' -> 'Next : Networking'.

![image](https://github.com/user-attachments/assets/ef7296c4-e998-4e9a-861c-c9da3e8d8a1d)

- Make sure the 'Virtual Network' is "Lab2-Vnet"

![image](https://github.com/user-attachments/assets/9bc6bcbc-21aa-4b95-89a5-3ce66ca6febc)

- Click 'Review + Create'.

![Screenshot 2025-05-03 010234](https://github.com/user-attachments/assets/afac077b-29c2-4129-9728-df473bd7ab1e)

- Wait for the VM to process till you see "Validation passed" on the top of the page.

![Screenshot 2025-05-06 141709](https://github.com/user-attachments/assets/d2a07be8-792c-4d0e-8065-400808893c07)

- Then click 'Create'.

![Screenshot 2025-05-06 141743](https://github.com/user-attachments/assets/f4d082cc-d5a0-4180-8efe-746d9dc2177f)

- You have successfully created a Linux Virtual Machine.
- Search for "Virtual Machines" and click on it, you will see both Virtual Machines.

![image](https://github.com/user-attachments/assets/8b17d796-e31e-4570-8886-6c40d2216545)

2. Observing ICMP Traffic

- On your PC, click the Windows 'start' icon to open up the menu and search up "Remote Desktop Connection".
- If using Mac, install Microsoft Remote Desktop.
- Copy windows VM Public IP address and paste it into the Remote Desktop Connection.
- Click 'Show Options' and put in the user name for the windows VM (labuser).
- Click 'connect'.


![image](https://github.com/user-attachments/assets/b876d3dd-1522-4957-ba07-854e5074a469)

- Insert the password then click 'ok'.
- Click 'Yes' to continue.

- Within your Windows 10 VM, go to Microsoft Edge and install Wireshark. Make sure to tick "Install Ncap version" during the installation process.

![Screenshot 2025-05-07 175629](https://github.com/user-attachments/assets/b81bf418-c396-42f8-8681-1dc45e335c40)

- Download the "Windows x64 Installer" then 'open file'.

![image](https://github.com/user-attachments/assets/45831a04-a397-4eae-b6e3-4b412b67827a)

- Click 'Next' -> 'Noted' -> 'Next'(until you see Packet Capture, make sure to tick "Install Ncap version" during the installation process) > 'Install'.
- On the License Agreement click 'I agree' -> Install -> Next -> Finish.

![image](https://github.com/user-attachments/assets/5af0f00e-5013-4409-b943-652a1d290545)

- Installation should be complete so click 'Next' and 'Finish'.

![image](https://github.com/user-attachments/assets/70424e65-daab-4d7f-8ca1-2ab0e69af2ac)

- On your Windows VM, click the Windows 'start' icon to open up the menu and search up "Wireshark" and open it.
- Click on 'Ethernet' and start a packet capture by clicking the small blue fin.

![image](https://github.com/user-attachments/assets/6b6babe7-8512-4181-a263-e08c0854de00)

- Apply filter for ICMP traffic only.

![image](https://github.com/user-attachments/assets/a40d53c5-1800-4cc2-9f37-792cef7b3fee)

- Retrieve/Copy the private IP address of the Linux VM on Azure (10.0.0.5).

![image](https://github.com/user-attachments/assets/5c78e569-78e7-4ae1-9ca6-715edfd984b1)

- Open up PoweShell on the Windows VM.

![image](https://github.com/user-attachments/assets/bfbb7b76-6c98-4db8-ac50-bc254a27bf65)

- Insert ping 10.0.0.5.

![image](https://github.com/user-attachments/assets/b9389d97-5d5c-4402-a16c-70ad3c0e4029)

- In PowerShell you can see there are four "replies" but in Wireshark window you see eight events because it captured both the request and reply from both sources (Windows and Linux). Furthermore, we know that we have successfully reached the destination over ICMP.

![image](https://github.com/user-attachments/assets/3a62665d-3921-4ea0-81a4-359a11581382)

3. Configuring a Firewall (Network Security Group)

- On Powershell type "ping 10.0.0.5 -t"
- A you will observe on Powershell it will have non-stop ping.

![image](https://github.com/user-attachments/assets/dc9724de-b5e0-44f6-a484-84c72a3df401)

- On your pc go to Azure Virtual Machines. Click on the Linux VM.
- Open the Networking panel and click on network settings.

![image](https://github.com/user-attachments/assets/ddb88c3b-3503-40f1-b0f9-f1314b2f00ee)

- Under essentials, where it says 'Network security group', click on 'linux-vm-nsg'

![image](https://github.com/user-attachments/assets/b6a090b6-fd93-4819-86cc-1d47689f7c86)

- Open the Networking coulumn and click on 'Inbound security rules' and configure the settings to block ICMP traffic
- Click 'Add'.
- 'Destination port ranges' put 'asterisk'.
- 'Protocol put 'ICMPv4'.
- 'Action' just click deny.
- 'Priority' to 290.
- Click 'Add'.

![image](https://github.com/user-attachments/assets/0f2bb2f3-110c-4f54-907a-f3af8b9f1af0)

- Back on Windows VM, when inspecting Powershell you will see it will have a "request timed out" and further inspection on Wireshark you will only see "request" meaning we have successfully created a virtual firewall rule to block incoming ICMP traffic to the Linux VM.

![image](https://github.com/user-attachments/assets/45c38493-0a02-4c11-8a93-1406cccb8376)

![image](https://github.com/user-attachments/assets/ecb0061c-2af2-414f-8d23-77e56f78f1f6)

- Follow previous steps to delete the new rule we just created on the Linux VM.

![image](https://github.com/user-attachments/assets/cd4462f2-51d9-45fe-8757-06835b4f49dc)

- Open the Linux VM in Azure and delete the block ICMP rule that we created. Click the trash can icon that is next to it to delete it.
- Switch back to the Windows 10 virtual machine and send a ping to the Linux VM. In Wireshark, you should see ICMP request and reply packets showing that the two VMs are communicating. In PowerShell, you’ll also see successful ping responses from the Linux VM. This confirms that ICMP communication between the VMs is working again.
- Stop the ping activity my pressing CTRL C.
- Stop the packet capture and close Wireshark.

![image](https://github.com/user-attachments/assets/f1a955a6-a9fe-46d7-b76d-f2838ceac3e2)

4. Observe SSH Traffic

- Back in Wireshark, start a packet capture up and filter for SSH traffic only in the display filter.

![image](https://github.com/user-attachments/assets/1071cc56-3cc4-4c88-bd53-893910dc51f9)

- On your PC, go to your virtual machine menu and click on Linux VM.
- Under Properties, obtain the Private IP address (10.0.0.5).

![image](https://github.com/user-attachments/assets/e2500917-8d5b-4e6e-b255-af390637f796)

- From the Windows 10 VM "SSH into" your Ubuntu VM (via its private IP address). Command will use the format ssh username@privateipaddress, for us is ssh labuser@10.0.0.5.
- Open up Powershell on your Windows 10 VM and type "ssh labuser@10.0.0.5" and it should generate SSH traffic.

![image](https://github.com/user-attachments/assets/ce4ab457-5077-44b4-98bf-7f60eaa6324b)

![image](https://github.com/user-attachments/assets/45ca8207-b9b2-4129-be0f-d75103145d92)

- On Powershell it will be asking if you want to continue connecting, just type "yes".
- It will asking for a password for your Linux VM password. (e.g, Cyberlab123!)
- When you type the paswword, nothing will appear. This is for security purposes.
- You will then notice the promt changed to "labuser@linux-vm" meaning to your are connected to the linux machine.

![image](https://github.com/user-attachments/assets/56b6f18b-5a76-456d-ae3c-09553b7c7fb0)

- Furthermore you can type "id" and "hostname" and "uname -a" to prove you are connected.

![image](https://github.com/user-attachments/assets/ca2d40d4-8f81-4a72-833a-c4174dbd696b)

- As you type commands, you'll see SSH traffic appear in Wireshark, with packets generated for each individual keystroke.
- When inspecting traffic the packets will appear encrypted because SSH establishes a secure, encrypted tunnel between the two systems.

![image](https://github.com/user-attachments/assets/042cad23-d3e9-4fed-bbb7-8a6ced0bd0c2)

- You can also type 'tcp.port ==22'. 
- Exit the SSH connection by typing ‘exit’ and pressing [Enter].
- If you check Wireshark you will notice it dropped the connection.

![image](https://github.com/user-attachments/assets/b32b3c62-7241-4075-8248-48fdcf7c5a5a)

![image](https://github.com/user-attachments/assets/48f65bf5-671e-405c-b946-a0dae719dfd1)

5. Observe DHCP Traffic

- Back in Wireshark, start a packet capture up and filter for DHCP traffic only in the display filter.

![image](https://github.com/user-attachments/assets/bf60a9d8-e49b-4df9-82c2-0cba554b0a49)

- Open notepad and enter the following:
- ipconfig /release
- ipconfig /renew

![image](https://github.com/user-attachments/assets/a43c3e84-3eda-4a34-ad32-9ab9fb7982e7)

- Click on File select 'Save As'.
- In the search bar type "c:\programdata" and press enter.

![image](https://github.com/user-attachments/assets/66453a72-be76-4812-a16e-4290ac9be903)

- As file type, save it as 'All Files'.
- File name is "dhcp.bat", then click Save.
- This script will execute commands to release the VM's current IP address and request a new one from the DHCP server, generating DHCP traffic that can be observed in Wireshark.

![image](https://github.com/user-attachments/assets/4bb9a7b0-32a8-4a1f-b37d-a2d4910c9104)

- Open PowerShell and type in "cd c:\programdata" then press enter.
- Then type "ls", meaning list.
- You will notice the file "dhcp.bat is listed.

![image](https://github.com/user-attachments/assets/29ea3c18-7b78-474f-ac41-696ed77cd069)

- On Wireshark type "udp.port == 67 || udp.port ==68" and press enter.

![image](https://github.com/user-attachments/assets/94d6778e-224c-4d7c-bf3d-a0f8a233749a)

- On Powershell type ".\dhcp.bat".
- Open Wireshark and observe the network traffic. You should see DHCP packets corresponding to the Discover -> Offer -> Request -> Acknowledge steps exchanged between the VM (10.0.0.4) and the DHCP server (168.63.129.16). The VM initiated the release of its IP address by sending a Release packet to the DHCP server using the ipconfig /release command. Then, ipconfig /renew is run next, which initiated the Discover -> Offer -> Request -> Acknowledge process between the VM and the DHCP server.
- Finished packet capture for dhcp.

![image](https://github.com/user-attachments/assets/00131353-3dc0-4427-8cfe-957f81cb2f9e)

![image](https://github.com/user-attachments/assets/d3c52e66-e3c1-423e-bc4e-b84fea76504c)

6. Observe DNS Traffic

- Back in Wireshark, start a packet capture and filter for DNS traffic only in the display filter.
- You can also filter for "udp.port == 53 || tcp.port == 53"

![image](https://github.com/user-attachments/assets/5a9998da-8a24-4af9-a306-22d8ae6dfd3b)

- On Powershell type up "nslookup disney.com".
- The Windows computer will reach out to the DNS and this command asks the DNS server for the IP address linked to a domain name and may show the DNS records associated with that domain.

![image](https://github.com/user-attachments/assets/cc666e8d-084c-441d-a4d7-833d89b7d2dd)

- Observe the DNS traffic in Wireshark. You can see the communication is captured between the VM (10.0.0.4) and the DNS Server (168.63.129.16).
- Our VM asked the DNS server for the IP address of disney.com, after some initial failed responses from the DNS server (indicated by the "No such same"), it eventually found the correct A record for disney.com and returned the IP 130.211.198.204 to us.
- Finished packet capture for DNS.

![image](https://github.com/user-attachments/assets/83650ded-e652-4a3a-980d-1a1956e616ec)

7. Observe RDP Traffic

- Remote Desktop Protocol (RDP) is a network communication protocol developed by Microsoft that allows users to remotely connect to and control another computer using a graphical user interface (GUI). It transmits the screen of the remote system to the local device and sends keyboard and mouse input back to the remote machine, enabling full interaction as if the user were physically present.
- Back in Wireshark, filter for RDP traffic only (tcp.port == 3389 || udp.port == 3389)
-  You will notice continuous packet transmission; this is because RDP is constantly streaming a picture from the server to your machine. 
- Finished packet capture for RDP.

![image](https://github.com/user-attachments/assets/f1d8f1d1-9469-4b7a-837f-58075f94007f)
