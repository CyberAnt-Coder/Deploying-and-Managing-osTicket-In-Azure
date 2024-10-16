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
![enablingIIS](https://imgur.com/g56GFHL.png)<br><br>
Also make sure to expand IIS -> World Wide Web Services -> Application Development Features and enable CGI. This allows us to install PHP Manager.<br>
### Installing Required Softwares
The files we need for this next step include PHPManagerForIIS_V1.5.0, rewrite_amd64_en-US, VC_redist.x86, and mysql-5.5.62-win32, php-7.3.8-nts-Win32-VC15-x86. All the files we use can be found by a google search but for ease of use all required downloads will be in this [doc](https://drive.google.com/drive/u/0/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6). Below is a picutre of all the files downloaded.<br> 
![downloadfile](https://imgur.com/parzeS9.png) <br><br>
PHP allows us to interact with the database we will create, the rewrite module converts search engine friendly URLs to a format that osTicket can use, and mySQL lets us create our database. First I downloaded and used PHPManagerForIIS_V1.5.0 and rewrite_amd64_en-US. Next I created a directory in the C drive. I named my PHP. <br>
![creatingdirectory](https://imgur.com/nxtcYnR.png)<br><br>
Into this directory I transfered all the files from the downloaded php-7.3.8-nts-Win32-VC15-x86. <br>
![transfer](https://imgur.com/uCXvjyh.png)This is important as PHP will need these files to use our database. After I used VC_redist.x86 and mysql-5.5.62-win32. When you open mysql-5.5.62-win32, there will be a bunch of configuration steps. Such as the following <br>
![standardconfig](https://imgur.com/pt1BsX0.png) <br><br>
I left these on basic/default options. Once you finish going through the configuration, you should end up on a tab that looks like this. <br>
![finshedsql](https://imgur.com/FQTRfzM.png) <br><br>
From here just click finish and we can move onto the next step.<br>

### Registering a New PHPversion
Next I opened up IIS as an admin. This is done by finding IIS in the taskbar and right clicking it to open it as an admin.<br>
![openingIISasadmin](https://imgur.com/4roQUZT.png)<br><br>
Then click on the PHP button on the second row to open the PHP Manager <br>
![PHPmanager](https://imgur.com/vRHSYd1.png)<br><br>
From here we click the blue link that says "Register new PHP version" and find the correct pathing to the php.cgi application. In my case it is the directory I created earlier, which is C:\PHP\php.cgi. <br>
![phpnewvers](https://imgur.com/U0ZmQdE.png)<br><br>

### Installing osTicket
Next, I downloaded the osTicket-v1.15.8 file found in the doc and moved just the upload folder into C:\inetpub\wwwroot.<br>
![osTicket](https://imgur.com/parzeS9.png)<br>
![move](https://imgur.com/J5eWC61.png)<br><br>
From here, rename the file from upload to osTicket. Now we can open osTicket in IIS. In IIS, click on the left side -> default websites -> osTicket. Then on the right side we can click on Browse*:80(http) This will open up a browser with the osTitcket installer. <br>
![osTicketbrowser](https://imgur.com/KTROvvb.png) ![browse80](https://imgur.com/TzViGwq.png)<br><br>
On the installer page, we can see that we are missing a few prerequisites. To fix this we must go back to IIS. First press on the PHP Manager button, then at the bottom click "enable or disable an extension".<br>
![extensions](https://imgur.com/YyTtzBA.png)<br><br>
From the installer, we can see the 3 extensions we are missing are the php_imap.dll, php_intl.dll, and the php_opcache.dll extensions. Enable all of these and refresh the page. From here we can see all the extensions we enabled now have a green checkmark next to them.<br>
![post](https://imgur.com/5h6xYGY.png)<br><br>
I skipped the bottom 2 as performance does not matter for us in this lab scenario. However if we were to use it for a real life application, I would recommend enabling these extensions as well. For the next step, I went back into the C:\inetpub\wwwroot directory and opened up the include folder. From here I found the ost-sampleconfig.php file. For the installer to use these files and install osTicket for us, we must give it temporary elavated privileges. To do so I opened the properties of the files, renamed it to ost-config, then I went to the security tab and disabled inheritence and clicked onto the advanced tab.<br>
![change name](https://imgur.com/vmGAujm.png)<br><br>
![properties](https://imgur.com/tVJ1Ze1.png)<br><br>
From here, I clicked "select a principle" and typed Everyone in the box below. Before clicking OK, it is important to check the names to ensure you are giving permissions to a real group.
![everyone](https://imgur.com/z6AmNjq.png)<br>
After I clicked OK, I checked the box that says Full control and clicked OK once again. 
![fullcontrol](https://imgur.com/eJQBs3b.png)<br><br>
After this apply your changes and then we can move onto the next step. We return to the osTicket installer and click continue. This takes us to a bunch of different fields for us to fill out ourselves. For my lab, I filled out the boxes as below.<br>
![osticketcresds](https://imgur.com/AeJJHMI.png)<br><br>
If there are any fields you are unsure how to fill out, be sure to use the ? mark beside each box to check what should go into each space. Make sure to take note of all the passwords and usernames used. Next I installed HeidiSQL from the doc from earlier. This will allow us to interact with our created database. Once you install HeidiSQL, and run it, click the New button on the bottom left side. Rename the new session as osTicket and enter in the admin username and password from the osTicket credential page.  
![heidisql](https://imgur.com/3FNv7pk.png)<br><br>
Now we can go back to the osTicket installer and press install now. If done correctly, we should be met with a congrats screen like so. <br>
![congrats](https://imgur.com/YhuhLUV.png)<br><br>
In most cases this would be finished but I wanted to double check if it was completed properly. So I 
![browse](https://imgur.com/TzViGwq.png)<br>
This takes us to a login page for osTicket. I then used the admin credentials to login. If everything is installed correctly we should be met with a page like this.
![doublecheck](https://imgur.com/cz3LDZ1.png)<br>
This is confirmation that we have finished installing osTicket on our VM. Next I started to setup the roles, departments and agents. 



