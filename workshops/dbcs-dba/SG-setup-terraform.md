Update January 22, 2018

# Setup

This workshop requires several setups steps that are normally done in advance as part of an automated process prior to running the labs.  In cases where a customer wishes to run through the workshop themselves on their own without the support of Global Services Engineering (GSE) they must first walk through the following steps.  

### **STEP 1**: Install Terraform and the Oracle Terraform OCI-Provider.

-	Download and install Terraform.  You have options for where you want to install it.  For the purposes of documenting the instructions we will use d:\tf directory.
```
https://www.terraform.io/downloads.html
```
 
 -	Download and install the Oracle 
```
https://github.com/oracle/terraform-provider-oci/releases
```

-	Copy the Terraform configuration files into your working directory.  The directory should look like this, with the terraform and oci provider and configuration files all on one directory.

	![](images/SG-setup-terraform/001.png)

### **STEP 2**: Generate SSH Key Pair on Windows Putty

-	Create a keys sub directory in your terraform directory.

	![](images/SG-setup-terraform/002.png)

-	Open Puttygen (available from the internet) and select generate a new key pair.

	![](images/SG-setup-terraform/011.png)

-	Then save the public key to your keys directory.

	![](images/SG-setup-terraform/012.png)

-	Name it publicKey for future reference.

	![](images/SG-setup-terraform/013.png)

-	Then save private key to your keys directory

	![](images/SG-setup-terraform/014.png)

-	Save it without a passphrase.

	![](images/SG-setup-terraform/015.png)

-	Name it `privateKey.ppk` for future reference.

	![](images/SG-setup-terraform/016.png)

-	Next export OpenSSH key for use on the Linux Image later in the labs.  In puttygen go to Conversions.

	![](images/SG-setup-terraform/016.1.png)

	![](images/SG-setup-terraform/016.2.png)

-	Name the key `privateKey`.  Note this version will NOT have the .ppk suffix that the private key you saved above.

	![](images/SG-setup-terraform/016.3.png)

### **STEP 3**: Generate Open SSL Key Pair on Windows

-	Download a Open SSL key generation program from the internet.  There are various sites.  The following can be used.
```
https://indy.fulgan.com/SSL/
```

-	Assuming you chose to use this key generation program (you can use your own), generate a private key.  Double click on the OpenSSL.exe file and then enter the following.  You will have to enter a pass phrase (make one up, at least four digits long).
```
genrsa -des3 -out oci_api_key.pem 2048
```
![](images/SG-setup-terraform/016.4.png)

![](images/SG-setup-terraform/016.5.png)

-	Generate the public key.  Enter the following
```
rsa -in oci_api_key.pem -outform PEM -pubout -out oci_api_key_public.pem
```
![](images/SG-setup-terraform/016.6.png)

-	Copy the pem keys from the OpenSSL directory to your /keys directory.  You should have five keys in the keys directory now: public and private OpenSSL pem keys, a public and private SSH key, and a private ppk Windows compatible key.

![](images/SG-setup-terraform/016.7.png)

![](images/SG-setup-terraform/016.8.png)

### **STEP 4**: Download the Oracle Backup Module, the Adobe Yum Repository, and the public yum ol6 repository to your Terraform directory (we are using D:\tf).

-	Go to the following site to download opc_installer.zip and save to your local download location:  `http://www.oracle.com/technetwork/database/availability/oracle-cloud-backup-2162729.html`

	![](images/SG-setup-terraform/025.png)

	![](images/SG-setup-terraform/026.png)

-	Go to the following site and download the adobe repository: `https://get.adobe.com/flashplayer/`.  Do not select the default - select download for a different operating system (Linux 64 bit).

	![](images/SG-setup-terraform/027.png)

-	Select the version YUM.

	![](images/SG-setup-terraform/028.png)

	![](images/SG-setup-terraform/029.png)

-	Go to `https://public-yum.oracle.com/public-yum-ol6.repo` and save the page results to your download directory.  

	![](images/SG-setup-terraform/029.1.png)

-	Then save to your download directory.  Name the file `public-yum-ol6.repo`.

	![](images/SG-setup-terraform/029.2.png)

### **STEP 6**: Copy files to the WorkshopImage.  See lower down if you are using a Linux OS.

-	Once the WorkshopImage is available log into the Cloud Console to obtain the Public IP address.

	![](images/SG-setup-terraform/030.png)

-	Open WinSCP (internet download) and log in to the IP address noted above from the Cloud Console with your private ppk key.

	![](images/SG-setup-terraform/031.png)

-	Ignore the warning

	![](images/SG-setup-terraform/032.png)

-	Select your private key.

	![](images/SG-setup-terraform/033.png)

	![](images/SG-setup-terraform/034.png)

-	Select yes to cache the key

	![](images/SG-setup-terraform/035.png)

-	Create directory /tmp/ws.  Go to Commands and select Open Terminal.  Ignore comment about Linux commands.

	![](images/SG-setup-terraform/035.1.png)

-	Enter the following and select Execute and then Close.
```
mkdir /tmp/ws
```
![](images/SG-setup-terraform/035.2.png)

![](images/SG-setup-terraform/035.3.png)

-	Change to the /tmp/ws directory and drag the following files from your download location to the /tmp/ws folder.

	![](images/SG-setup-terraform/036.png)

### **STEP 7**: Log into your WorkshopImage and run install scripts

-	Open Putty and log into the DBCS instance.

	![](images/SG-setup-terraform/039.png)

-	Select your SSH private privatekey.ppk key.

	![](images/SG-setup-terraform/040.png)

-	Log in as opc

	![](images/SG-setup-terraform/041.png)

	![](images/SG-setup-terraform/042.png)

-	Change file permissions.  Enter the following:
```
chmod a+rw /tmp/ws/*
```

-	Install the yum repository and then adobe and git packages.  Enter the following:
```
sudo su -
cp /tmp/ws/public-yum-ol6.repo /etc/yum.repos.d
rpm -ivh /tmp/ws/adobe-release-x86_64-1.0-1.noarch.rpm
yum -y install svn
exit
```
![](images/SG-setup-terraform/044.png)

-	Log in as Oracle and copy the install files from the DBCS Workshop git.
```
. oraenv (enter ORCL when prompted)
sudo su - oracle
svn export https://github.com/pcdavies/DatabaseCloudServiceForDBAsOCI/trunk/workshops/dbcs-dba/install/install_oci.zip
unzip install_oci.zip
exit
```
![](images/SG-setup-terraform/045.png)

-	Log back in as root and install the desktop packages.  Enter the following.
```
sudo su -
/tmp/ws/oci_ws_yum.sh
```
![](images/SG-setup-terraform/046.png)

-	While we are logged in as root we also need to open the VNC server port that we'll be starting in the next step.  Enter the following.
```
sed -i 's/IPTABLES_SAVE_ON_RESTART="no"/IPTABLES_SAVE_ON_RESTART="yes"/g' /etc/sysconfig/iptables-config
iptables -I INPUT -p tcp -m tcp --dport 5901 -j ACCEPT
service iptables restart
```
![](images/SG-setup-terraform/058.png)

-	Start VNC Server.  You can optionally adjust the geometry to match your screen (eg: `vncserver -geometry 1280x720`).  You will be prompted to enter a password.  Do not use the password that we have been specifying in other places in this lab document.  **VNC is open to the internet.  Select your own secure password.  We suggest you use your cloud password.**.  Be sure you are sudo su to oracle user.
```
exit (this is to exit out of the root user)
sudo su - oracle
vncserver
```
![](images/SG-setup-terraform/047.png)

### **STEP 8**: Open Port 5901 (VNC) and log into the desktop

-	Log back into the Cloud Console and select Database Service.

	![](images/SG-setup-terraform/048.png)

	![](images/SG-setup-terraform/049.png)

-	Select Networking - Virtual Cloud Networks.

	![](images/SG-setup-terraform/050.png)

-	Select the Virtual Cloud Network you created earlier.

	![](images/SG-setup-terraform/051.png)

-	Select Security Lists.

	![](images/SG-setup-terraform/052.png)

-	Select Default Security List.

	![](images/SG-setup-terraform/053.png)

-	Select Edit All Rules.

	![](images/SG-setup-terraform/054.png)

-	Add Ingress Rule opening port 5901.

	![](images/SG-setup-terraform/055.png)

-	Enter the following details. 

	![](images/SG-setup-terraform/056.png)

-	Scroll down and save the rules.  Note the rule will take about a minute to become active.

	![](images/SG-setup-terraform/057.png)

-	Start your VNC **Viewer Client** and log in with the password you entered above.  Use your own WorkshopImage IP address (not the one in the screenshot below).

	![](images/SG-setup-terraform/059.png)

-	This is the WorkshopImage desktop.  The background may be different across the screen shots.  We need to disable the screen saver to prevent a screen unlock prompt.  Go to the system menu on the desktop and de-activate screen saver.  If you delay in this step and the screen locks up you will need to kill the vncserver (`vncserver -kill :1`) and restart it (`vncserver`) in your terminal window.

	![](images/SG-setup-terraform/060.png)

	![](images/SG-setup-terraform/061.png)

### **STEP 9**: Set up shortcut to SQLDeveloper and import connections

-	Right click on the desktop and create a new launcher on the desktop.  Enter the Name and Command:
	- **Name:** `SqlDeveloper`
	- **Command:** `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/sqldeveloper/bin/sqldeveloper`

	![](images/SG-setup-terraform/062.png)

	![](images/SG-setup-terraform/063.png)

-	Select the sqldeveloper icon from the following directory: `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/`

	![](images/SG-setup-terraform/064.png)

	![](images/SG-setup-terraform/065.png)

-	Double click on the desktop icon to start sqldeveloper. Select no to import connections.

	![](images/SG-setup-terraform/066.png)

-	Right click on connections and select import connections.

	![](images/SG-setup-terraform/067.png)

-	Browse for connections file.

	![](images/SG-setup-terraform/068.png)

	![](images/SG-setup-terraform/069.png)

	![](images/SG-setup-terraform/070.png)

	![](images/SG-setup-terraform/071.png)

### **STEP 10**: Import data

-	Update the tnsnames.ora file to add pdb1 and new_pdb (used later).  Open a new terminal window and enter the following:
```
gedit /u01/app/oracle/product/12.2.0.1/dbhome_1/network/admin/tnsnames.ora
```

![](images/SG-setup-terraform/072.png)

-	Copy the CDB connection entry into two more entries that we'll modify for pdb1 and new_pdb.  Update as follows.  Then save.

	![](images/SG-setup-terraform/073.png)

-	Open a terminal window and enter the following:
```
. oraenv (enter ORCL when prompted)
./install_oci.sh
```

![](images/SG-setup-terraform/074.png)