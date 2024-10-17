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
To begin we first create the virtual machine through the Microsoft Azure portal. We are using a virtual machine in order to keep our personal computers safe as well as having a clean setup to install only the necessary software for running osTicket. For the resource group, it can be named anything we please, but I set my resource group as “osTicket_Lab” and set the size to “Standard B2s v2 (2 vcpus, 8Gib memory)”. This lab is possible with different sizes, but I recommend at least 2 vcpus as when using only 1 vcpu, it is very slow. The rest of the settings can be left default but make sure to leave port 3389 open to allow us to access the Vm through Microsoft Remote Desktop. <br>
![vmsetup](https://imgur.com/Fr3U0QR.png)<br>

### Using Microsoft Remote Desktop to Connect to the VMs
Next, we connect to the virtual machine using Microsoft Remote Desktop. Open Microsoft Remote Desktop application on our physical computer by searching it in the task bar, and connect to the virtual machine using the IPv4/public IP address. <br>
![RDC](https://imgur.com/rI8aqFE.png) <br>

### Enabling IIS
Next, we need to enable Internet Information Services on our VM. To do this we find the control panel in the task bar and click on programs -> programs and features -> turn Windows features on or off. From here we then check the box that says Internet Information Services. Since osTicket runs out of a website, enabling IIS allows us to use it. <br>
![enablingIIS](https://imgur.com/g56GFHL.png)<br>
Also in the same menu, make sure to expand IIS -> World Wide Web Services -> Application Development Features and enable CGI. This will allow us to install PHP Manager.<br>
### Installing Required Softwares
The files we need for this next step are PHPManagerForIIS_V1.5.0, rewrite_amd64_en-US, VC_redist.x86, and mysql-5.5.62-win32, php-7.3.8-nts-Win32-VC15-x86. All the files we use can be found by a google search but for ease of use all required downloads will be in this [doc](https://drive.google.com/drive/u/0/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6). Below is a picture of all files used in this lab.<br>
![downloadfile](https://imgur.com/parzeS9.png) <br>
PHP allows us to interact with the database we will create, the rewrite module converts search engine friendly URLs to a format that osTicket can use, and mySQL lets us create our database. First we download and use PHPManagerForIIS_V1.5.0 and rewrite_amd64_en-US. Next we created a directory in the C drive. I named my PHP. <br>
![creatingdirectory](https://imgur.com/nxtcYnR.png)<br>
Into this directory we transfer all the files from the downloaded php-7.3.8-nts-Win32-VC15-x86. <br>
![transfer](https://imgur.com/uCXvjyh.png)<br>
This is important as PHP will need these files to use our database. After that we use VC_redist.x86 and mysql-5.5.62-win32. When we open mysql-5.5.62-win32, there will be a bunch of configuration steps. Such as the following <br>
![standardconfig](https://imgur.com/pt1BsX0.png) <br>
It will ask you to create a username and password as well as tweak some other settings. For the username and password we can put anything, but it is important to write these down somewhere so we can use them later. As for the settings, these can be left on basic/default options. Once we finish going through the configuration, we should be met with a screen that looks like this. <br>
![finshedsql](https://imgur.com/FQTRfzM.png) <br>
From here just click finish and we can move onto the next step.<br>

### Registering a New PHPversion
Next we need to open up IIS as an admin. This is done by finding IIS in the taskbar and right clicking it to open it as an admin.<br>
![openingIISasadmin](https://imgur.com/4roQUZT.png)<br>
Then click on the PHP button on the second row to open the PHP Manager <br>
![PHPmanager](https://imgur.com/vRHSYd1.png)<br>
From here we click the blue link that says "Register new PHP version" and find the correct pathing to the php.cgi application. In my case it is the PHP directory we created earlier. In my case this is C:\PHP\php.cgi. <br>
![phpnewvers](https://imgur.com/U0ZmQdE.png)<br>

### Installing osTicket
Next we download the osTicket-v1.15.8 file found in the doc and move just the upload folder into C:\inetpub\wwwroot.<br>
![osTicket](https://imgur.com/parzeS9.png)<br>
![move](https://imgur.com/J5eWC61.png)<br>
From here, rename the file from upload to osTicket. Now we can open osTicket in IIS. In IIS, click on the left side -> default websites -> osTicket. Then on the right side we can click on Browse*:80(http) This will open up a browser with the osTitcket installer. <br>
![osTicketbrowser](https://imgur.com/KTROvvb.png) ![browse80](https://imgur.com/TzViGwq.png)<br>
On the installer page, we can see that we are missing a few prerequisites. To fix this we must go back to IIS. First press on the PHP Manager button, then at the bottom click "enable or disable an extension".<br>
![extensions](https://imgur.com/YyTtzBA.png)<br>
From the installer, we can see the 3 extensions we are missing are the php_imap.dll, php_intl.dll, and the php_opcache.dll extensions. Enable all of these and refresh the page. From here we can see all the extensions we enabled now have a green checkmark next to them.<br>
![post](https://imgur.com/5h6xYGY.png)<br>
For now we can skip the bottom 2 extensions as performance does not matter for us in this lab scenario. However if we were to use it for a real life application, I would recommend enabling these extensions as well. For the next step, we can go back into the C:\inetpub\wwwroot directory and open up the include folder. From here we find the ost-sampleconfig.php file. For the installer to use these files and install osTicket for us, we must give it temporary elevated privileges. To do so we open the properties of the files, rename it to ost-config, then go back to the security tab, disable inheritance and click onto the advanced tab.<br>
![change name](https://imgur.com/vmGAujm.png)<br><br>
![properties](https://imgur.com/tVJ1Ze1.png)<br>
From here, we click "select a principle" and type "Everyone” in the box below. Before clicking OK, it is important to check the names to ensure you are giving permissions to a real group.
![everyone](https://imgur.com/z6AmNjq.png)<br>
After we click OK, we check the box that says Full control and click OK once again. 
![fullcontrol](https://imgur.com/eJQBs3b.png)<br>
After this apply our changes and then we can move onto the next step. We return to the osTicket installer and click continue. This takes us to a bunch of different fields for us to fill out ourselves. For my lab, I filled out the boxes as below.<br>
![osticketcresds](https://imgur.com/AeJJHMI.png)<br>
If there are any fields you are unsure how to fill out, be sure to use the ? mark beside each box to check what should go into each space. Make sure to take note of all the passwords and usernames used. Next we install HeidiSQL from the doc from earlier. This will allow us to interact with our created database. Once we install HeidiSQL, and run it, click the New button on the bottom left side. Rename the new session as osTicket and enter in the admin username and password from the osTicket credential page. <br>
![heidisql](https://imgur.com/3FNv7pk.png)<br>
Now we can go back to the osTicket installer and press install now. If done correctly, we should be met with a congrats screen like so. <br>
![congrats](https://imgur.com/YhuhLUV.png)<br>
In most cases this lab would be finished but I wanted to double check if it was completed properly. So I went to the [admin login page](http://localhost/osTicket/scp/login.php) for osTicket and used the admin credentials to login. If everything is installed correctly we should be met with a page like this.
![doublecheck](https://imgur.com/cz3LDZ1.png)<br>
This is confirmation that we have finished installing osTicket on our VM. Next I started to set up the roles, departments and agents. 



