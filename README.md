# Deploying-and-Managing-osTicket-In-Azure

## Description

## Technologies Used
  - Microsoft Azure
  - Microsoft Remote Desktop
  - Internet Information Services
  - osTicket
  - HeidiSQL

## Walkthrough

### Setting Up Virtual Machines
To begin I first created the virtual machines using Microsoft Azure. I decided to use a virtual machine to keep my personal computer safe and to have a clean setup to install only the necessary software. Most of the information here can be changed, but I set my resource group as “osTicket_Lab”, and set the size to “Standard B2s v2 (2 vcpus, 8 GiB memory)”. This lab is possible with different sizes, but I recommend at least 2 vcpus as when using only 1 vcpu, it is very slow.  Also make sure to leave port 3389 open to allow Microsoft Remote Desktop. <br>
![vmsetup](https://imgur.com/Fr3U0QR.png)<br>

### Using Microsoft Remote Desktop to Connect to the VMs
Next, I connected to the virtual machine using Microsoft Remote Desktop. First I opened Microsoft Remote Desktop application on my physical computer, and connected it using the IPv4 or public IP address. <br>
![RDC](https://imgur.com/rI8aqFE.png) <br>

### Enabling IIS
Next, on my VM I enabled Internet Information Services. For a Windows 10 computer it is located in the control panel -> programs -> programs and features -> then click on the side turn windows features on or off. From there you check the box that says Internet Information Services. This will allow us to run osTicket out of the website.<br>
![enablingIIS](https://imgur.com/g56GFHL.png)<br>
Also make sure to expand IIS -> World Wide Web Services -> Application Development Features and enable CGI. This allows us to install PHP Manager.<br>
### Installing PHP
Next we use the included link to install PHPManagerForIIS. This can be found by a google search but for ease of use all required downloads will be in this [doc](https://drive.google.com/drive/u/0/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6). PHP allows us to interact with the database we will create, and the rewrite module converts search engine friendly URLs to a format that osTicket can use. <br>
![PHP and rewrite](https://imgur.com/hcHK49I.png)

### Installing osTicket

