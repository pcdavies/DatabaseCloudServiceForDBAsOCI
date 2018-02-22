Update January 22, 2018

# Setup

This workshop requires several setups steps that are normally done in advance as part of an automated process prior to running the labs.  In cases where a customer wishes to run through the workshop themselves on their own without the support of Global Services Engineering (GSE) they must first walk through the following steps.  

### **STEP 1**: Log into the Cloud Console

The following creates a new DBCS Enterprise instance with backup to cloud.  Use your own SSH key or generate a new one.  

-	Log into your identitity domain/trial account.

	![](images/setup/001.png)

-	Customize dashboard if you do not see any current services.

	![](images/setup/002.png)

-	Select show Database.  Be sure to select **Database (OCI)** and not Database

	![](images/setup/003.png)

-	Select Database (OCI), and then Open Service Console

	![](images/setup/004.png)

	![](images/setup/005.png)

### **STEP 2**: Create Virtual Cloud Network

-	Select Networking, then Virtual Cloud Netowork.

	![](images/setup/006.png)

-	Create Virtual Cloud Network.  Be sure to select the correct Compartment (ie. not root or Managed Compartment for PaaS)

	![](images/setup/007.png)

-	Be sure to select CREATE VIRTUAL CLOUD NETWORK **PLUS RELATED RESOURCES**

	![](images/setup/008.png)

-	Take defaults thereafter.

	![](images/setup/009.png)

	![](images/setup/010.png)

### **STEP 3**: Generate SSH Key Pair

-	When you create a DBCS instance (next step) you will be prompted for a public key.  This needs to be generated in advance.  Open Puttygen (available from the internet) and select generate a new key pair.

	![](images/setup/011.png)

-	Then save the public key.

	![](images/setup/012.png)

-	Name it publicKey for future reference.

	![](images/setup/013.png)

-	Then save private key.

	![](images/setup/014.png)

-	Save it without a passphrase.

	![](images/setup/015.png)

-	Name it `privateKey` for future reference.

	![](images/setup/016.png)

-	Next export OpenSSH key for use on the Linux Image later in the labs.  In puttygen go to Conversions.

	![](images/setup/016.1.png)

	![](images/setup/016.2.png)

-	Name the key `privateKey`.  Note this version will NOT have the .ppk suffix that the private key you saved above.

	![](images/setup/016.3.png)

### **STEP 4**: Create **Workshop** Image (DBCS Instance)

-	Select DB System from top menu.

	![](images/setup/017.png)

-	Then Launch DB System

	![](images/setup/018.png)

-	Enter details.  Your resources may vary by Availability Domain.  In somecases you may not have available capacity in a Domain, and if this is the case select another Domain.

	![](images/setup/019.png)

-	Choose ssh key file.

	![](images/setup/020.png)

-	Select `publicKey`.

	![](images/setup/021.png)

-	Enter Database information as shown in screen shot.  Enter a password of `ALpha2018__` (note there are 2 underscores at the end).

	![](images/setup/022.png)

-	Then Launch DB System.

	![](images/setup/023.png)

-	The system will begin provisioning.  This can take an hour or more.

	![](images/setup/024.png)

### **STEP 5**: Download the Oracle Backup Module, the Adobe Yum Repository, and the public yum ol6 repository

-	Go to the following site to download opc_installer.zip and save to your local download location:  `http://www.oracle.com/technetwork/database/availability/oracle-cloud-backup-2162729.html`

	![](images/setup/025.png)

	![](images/setup/026.png)

-	Go to the following site and download the adobe repository: `https://get.adobe.com/flashplayer/`.  Do not select the default - select download for a different operating system (Linux 64 bit).

	![](images/setup/027.png)

-	Select the version YUM.

	![](images/setup/028.png)

	![](images/setup/029.png)

-	Go to `https://public-yum.oracle.com/public-yum-ol6.repo` and save the page results to your download directory.  

	![](images/setup/029.1.png)

-	Then save to your download directory.  Name the file `public-yum-ol6.repo`.

	![](images/setup/029.2.png)

### **STEP 6**: Copy files to the WorkshopImage

-	Once the WorkshopImage is available log into the Cloud Console to obtain the IP address.

	![](images/setup/030.png)

-	Open WinSCP (internet download) and log in to the IP address noted above from the Cloud Console with your private ppk key.

	![](images/setup/031.png)

-	Ignore the warning

	![](images/setup/032.png)

-	Select your private key.

	![](images/setup/033.png)

	![](images/setup/034.png)

-	Select yes to cache the key

	![](images/setup/035.png)

-	Drag the following files from your download location to the oracle folder.

	![](images/setup/036.png)

	![](images/setup/037.png)

	![](images/setup/038.png)

### **STEP 7**: Log into your WorkshopImage and run install scripts

-	Open Putty and log into the DBCS instance.

	![](images/setup/039.png)

-	Select your SSH private privatekey.ppk key.

	![](images/setup/040.png)

-	Log in as opc

	![](images/setup/041.png)

	![](images/setup/042.png)

-	Copy the files to the /tmp directory.  Enter the following:
	- `chmod a+rw *`
	- `mv publicKey publicKey.pub`
	- `cp * /tmp`

	![](images/setup/043.png)

-	Install the yum repository and then adobe and git packages.  Enter the following:
```
sudo su -
cp /tmp/public-yum-ol6.repo /etc/yum.repos.d
rpm -ivh /tmp/adobe-release-x86_64-1.0-1.noarch.rpm
yum -y install svn
exit
```
![](images/setup/044.png)

-	Log in as Oracle and clone the DBCS Workshop git.
```
sudo su - oracle
svn export https://github.com/pcdavies/DatabaseCloudServiceForDBAsOCI/trunk/workshops/dbcs-dba/install/install_oci.zip
unzip install_oci.zip
/home/oracle/install_oci.sh
exit
```
![](images/setup/045.png)

-	Log back in as root and install the desktop packages.  Enter the following.
```
sudo su -
/home/oracle/yum.sh
```
![](images/setup/046.png)

-	While we are logged in as root we also need to open the VNC server port that we'll be starting in the next step.  Enter the following.
	- `sed -i 's/IPTABLES_SAVE_ON_RESTART="no"/IPTABLES_SAVE_ON_RESTART="yes"/g' /etc/sysconfig/iptables-config`
	- `iptables -I INPUT -p tcp -m tcp --dport 5901 -j ACCEPT`
	- `service iptables restart`

	![](images/setup/046.png)

-	Start VNC Server.  You can optionally adjust the geometry to match your screen (eg: `vncserver -geometry 1280x720`).  You will be prompted to enter a password.  Do not use the password that we have been specifying in other places in this lab document.  **VNC is open to the internet.  Select your own secure password.  We suggest you use your cloud password.**.  Be sure you are sudo su to oracle user.
	- `exit` - this is exist out of the root user
	`sudo su - oracle`
	- `vncserver`

	![](images/setup/047.png)

### **STEP 8**: Open Port 5901 (VNC) and log into the desktop

-	Log back into the Cloud Console and select Database Service.

	![](images/setup/048.png)

	![](images/setup/049.png)

-	Select Networking - Virtual Cloud Networks.

	![](images/setup/050.png)

-	Select the Virtual Cloud Network you created earlier.

	![](images/setup/051.png)

-	Select Security Lists.

	![](images/setup/052.png)

-	Select Default Security List.

	![](images/setup/053.png)

-	Select Edit All Rules.

	![](images/setup/054.png)

-	Add Ingress Rule opening port 5901.

	![](images/setup/055.png)

-	Enter the following details. 

	![](images/setup/056.png)

-	Scroll down and save the rules.  Note the rule will take about a minute to become active.

	![](images/setup/057.png)

-	Start your VNC **Viewer Client** and log in with the password you entered above.  Use your own WorkshopImage IP address (not the one in the screenshot below).

	![](images/setup/059.png)

-	This is the WorkshopImage desktop.  The background may be different across the screen shots.

	![](images/setup/143.png)

-	We need to disable the screen saver to prevent a screen unlock prompt.  Go to the system menu on the desktop and de-activate screen saver.  If you delay in this step and the screen locks up you will need to kill the vncserver (`vncserver -kill :1`) and restart it (`vncserver`) in your terminal window.

	![](images/setup/060.png)

	![](images/setup/061.png)

### **STEP 6**: Set up shortcut to SQLDeveloper and import connections

-	Right click on the desktop and create a new launcher on the desktop.  Enter the Name and Command:
	- **Name:** `SqlDeveloper`
	- **Command:** `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/sqldeveloper/bin/sqldeveloper`

	![](images/setup/062.png)

	![](images/setup/063.png)

-	Select the sqldeveloper icon from the following directory: `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/`

	![](images/setup/064.png)

	![](images/setup/065.png)

-	Double click on the desktop icon to start sqldeveloper. Select no to import connections.

	![](images/setup/066.png)

-	Right click on connections and select import connections.

	![](images/setup/067.png)

-	Browse for connections file.

	![](images/setup/068.png)

	![](images/setup/069.png)

	![](images/setup/070.png)

	![](images/setup/071.png)
