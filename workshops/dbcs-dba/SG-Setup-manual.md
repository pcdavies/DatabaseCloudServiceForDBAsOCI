Update January 22, 2018

# Setup

This workshop requires several setups steps that are normally done in advance as part of an automated process prior to running the labs.  In cases where a customer wishes to run through the workshop themselves on their own without the support of Global Services Engineering (GSE) they must first walk through the following steps.  

### **STEP 1**: Log into the Cloud Console

The following creates a new DBCS Enterprise instance with backup to cloud.  Use your own SSH key or generate a new one.  

-	Log into your identitity domain/trial account.

	![](images/SG-setup-manual/001.png)

-	Customize dashboard if you do not see any current services.

	![](images/SG-setup-manual/002.png)

-	Select show Database.  Be sure to select **Database (OCI)** and not Database

	![](images/SG-setup-manual/003.png)

-	Select Database (OCI), and then Open Service Console

	![](images/SG-setup-manual/004.png)

	![](images/SG-setup-manual/005.png)

### **STEP 2**: Create Virtual Cloud Network

-	Select Networking, then Virtual Cloud Netowork.

	![](images/SG-setup-manual/006.png)

-	Create Virtual Cloud Network.  Be sure to select the correct Compartment (ie. not root or Managed Compartment for PaaS)

	![](images/SG-setup-manual/007.png)

-	Enter the following and then select `Create Virtual Cloud Network`:
	- **Compartment:** `Demo`
	- **Name:** `vcn1`
	- **CIDR BLOCK:** `10.0.0.0/16`

	![](images/SG-setup-manual/008.png)

-	Next Create subnets.  We will create a subnet for each of the availability zones 1-3.

	![](images/SG-setup-manual/008.1.png)

-	Enter the following:
	- **Name:** `subnet1`
	- **Availability Zone:** `<select the first one>`
	- **CIDR BLOCK:** `10.0.0.0/24`
	- **ROUTE TABLE:** `Default Route Table for vcn1`
	- accept defaults for the remaining fields, scroll to the bottom, and select 

	![](images/SG-setup-manual/008.2.png)

	![](images/SG-setup-manual/008.3.png)

-	Create subnet2 and subnet3 along similar lines.  The CIDR BLOCK and availability zones are:
	- **subnet2:** `CIDR BLOCK 10.0.1.0/24, then select the second availability zone`
	- **subnet3:** `CIDR BLOCK 10.0.2.0/24, then select the third availability zone`

-	The result should look like this.

	![](images/SG-setup-manual/009.png)

### **STEP 3**: Generate SSH Key Pair on Windows Putty (see lower down to generate on Linux O/S).

-	When you create a DBCS instance (next step) you will be prompted for a public key.  This needs to be generated in advance.  Open Puttygen (available from the internet) and select generate a new key pair.

	![](images/SG-setup-manual/011.png)

-	Then save the public key.

	![](images/SG-setup-manual/012.png)

-	Name it publicKey for future reference.

	![](images/SG-setup-manual/013.png)

-	Then save private key.

	![](images/SG-setup-manual/014.png)

-	Save it without a passphrase.

	![](images/SG-setup-manual/015.png)

-	Name it `privateKey` for future reference.

	![](images/SG-setup-manual/016.png)

-	Next export OpenSSH key for use on the Linux Image later in the labs.  In puttygen go to Conversions.

	![](images/SG-setup-manual/016.1.png)

	![](images/SG-setup-manual/016.2.png)

-	Name the key `privateKey`.  Note this version will NOT have the .ppk suffix that the private key you saved above.

	![](images/SG-setup-manual/016.3.png)

-	IF you are running a Linux OS use the following commands to generate a key pair.  This generates sshkey and sshkey.pub. Note you will need to rename these to privateKey and PublicKey to avoid confusion later in the labs.
	- `ssh-keygen` (name it sshkey, hit enter to accept without a password)
	- `chmod 600 sshkey` (change permissions of the private key)

### **STEP 4**: Create **WorkshopImage** Image (DBCS Instance)

-	Select DB System from top menu.

	![](images/SG-setup-manual/017.png)

-	Then Launch DB System

	![](images/SG-setup-manual/018.png)

-	Enter details.  Your resources may vary by Availability Domain.  In somecases you may not have available capacity in a Domain, and if this is the case select another Domain.

	![](images/SG-setup-manual/019.png)

-	Choose ssh key file.

	![](images/SG-setup-manual/020.png)

-	Select `publicKey`.

	![](images/SG-setup-manual/021.png)

-	Enter Database information as shown in screen shot.  Enter a password of `ALpha2018__` (note there are 2 underscores at the end).

	![](images/SG-setup-manual/022.png)

-	Then Launch DB System.

	![](images/SG-setup-manual/023.png)

-	The system will begin provisioning.  This can take an hour or more.

	![](images/SG-setup-manual/024.png)

### **STEP 5**: Create **Alpha01A-DBCS** Image (DBCS Instance)

We will create a second DBCS instance to support lab200 activities that involve migrating data between instances.  This second instance will be nearly identical to the first with the following exceptions.  Refer to the steps above for WorkshopImage for the steps, taking note to change the following:

- **Display Name:** `Alpha01A-DBCS`
- **Availability Zone:** `Select the third zone, or if using a customer account, one that has the capacity for an instance`
- **Client Subnet:** `subnet3`

	![](images/SG-setup-manual/024.1.png)

	![](images/SG-setup-manual/024.2.png)

	![](images/SG-setup-manual/024.3.png)

### **STEP 5**: Download the Oracle Backup Module, the Adobe Yum Repository, and the public yum ol6 repository

-	Go to the following site to download opc_installer.zip and save to your local download location:  `http://www.oracle.com/technetwork/database/availability/oracle-cloud-backup-2162729.html`

	![](images/SG-setup-manual/025.png)

	![](images/SG-setup-manual/026.png)

-	Go to the following site and download the adobe repository: `https://get.adobe.com/flashplayer/`.  Do not select the default - select download for a different operating system (Linux 64 bit).

	![](images/SG-setup-manual/027.png)

-	Select the version YUM.

	![](images/SG-setup-manual/028.png)

	![](images/SG-setup-manual/029.png)

-	Go to `https://public-yum.oracle.com/public-yum-ol6.repo` and save the page results to your download directory.  

	![](images/SG-setup-manual/029.1.png)

-	Then save to your download directory.  Name the file `public-yum-ol6.repo`.

	![](images/SG-setup-manual/029.2.png)

### **STEP 6**: Copy files to the WorkshopImage.  See lower down if you are using a Linux OS.

-	Once the WorkshopImage is available log into the Cloud Console to obtain the Public IP address.

	![](images/SG-setup-manual/030.png)

-	Open WinSCP (internet download) and log in to the IP address noted above from the Cloud Console with your private ppk key.

	![](images/SG-setup-manual/031.png)

-	Ignore the warning

	![](images/SG-setup-manual/032.png)

-	Select your private key.

	![](images/SG-setup-manual/033.png)

	![](images/SG-setup-manual/034.png)

-	Select yes to cache the key

	![](images/SG-setup-manual/035.png)

-	Drag the following files from your download location to the oracle folder.

	![](images/SG-setup-manual/036.png)

	![](images/SG-setup-manual/037.png)

	![](images/SG-setup-manual/038.png)

-	IF you are using a Linux desktop use the following command (running from the directory where you staged the files).  Otherwise if you are on Windows disregard this step.
```
scp -i privateKey * opc@<WorkshopImage Pubic IP>:.
```

### **STEP 7**: Log into your WorkshopImage and run install scripts

-	Open Putty and log into the DBCS instance.

	![](images/SG-setup-manual/039.png)

-	Select your SSH private privatekey.ppk key.

	![](images/SG-setup-manual/040.png)

-	Log in as opc

	![](images/SG-setup-manual/041.png)

	![](images/SG-setup-manual/042.png)

-	Copy the files to the /tmp/ws directory.  Enter the following:
```
chmod a+rw *
mv publicKey publicKey.pub
mkdir /tmp/ws
cp * /tmp/ws
```

![](images/SG-setup-manual/043.png)

-	Install the yum repository and then adobe and git packages.  Enter the following:
```
sudo su -
cp /tmp/ws/public-yum-ol6.repo /etc/yum.repos.d
rpm -ivh /tmp/ws/adobe-release-x86_64-1.0-1.noarch.rpm
yum -y install svn
exit
```
![](images/SG-setup-manual/044.png)

-	Log in as Oracle and copy the install files from the DBCS Workshop git.
```
. oraenv (enter ORCL when prompted)
sudo su - oracle
svn export https://github.com/pcdavies/DatabaseCloudServiceForDBAsOCI/trunk/workshops/dbcs-dba/install/install_oci.zip
unzip install_oci.zip
exit
```
![](images/SG-setup-manual/045.png)

-	Log back in as root and install the desktop packages.  Enter the following.
```
sudo su -
/home/oracle/yum.sh
```
![](images/SG-setup-manual/046.png)

-	While we are logged in as root we also need to open the VNC server port that we'll be starting in the next step.  Enter the following.
```
sed -i 's/IPTABLES_SAVE_ON_RESTART="no"/IPTABLES_SAVE_ON_RESTART="yes"/g' /etc/sysconfig/iptables-config
iptables -I INPUT -p tcp -m tcp --dport 5901 -j ACCEPT
service iptables restart
```

![](images/SG-setup-manual/058.png)

-	Start VNC Server.  You can optionally adjust the geometry to match your screen (eg: `vncserver -geometry 1280x720`).  You will be prompted to enter a password.  Do not use the password that we have been specifying in other places in this lab document.  **VNC is open to the internet.  Select your own secure password.  We suggest you use your cloud password.**.  Be sure you are sudo su to oracle user.
```
exit (this is to exit out of the root user)
sudo su - oracle
vncserver
```

![](images/SG-setup-manual/047.png)

### **STEP 8**: Open Port 5901 (VNC) and log into the desktop

-	Log back into the Cloud Console and select Database Service.

	![](images/SG-setup-manual/048.png)

	![](images/SG-setup-manual/049.png)

-	Select Networking - Virtual Cloud Networks.

	![](images/SG-setup-manual/050.png)

-	Select the Virtual Cloud Network you created earlier.

	![](images/SG-setup-manual/051.png)

-	Select Security Lists.

	![](images/SG-setup-manual/052.png)

-	Select Default Security List.

	![](images/SG-setup-manual/053.png)

-	Select Edit All Rules.

	![](images/SG-setup-manual/054.png)

-	Add Ingress Rule opening port 5901.

	![](images/SG-setup-manual/055.png)

-	Enter the following details. 

	![](images/SG-setup-manual/056.png)

-	Scroll down and save the rules.  Note the rule will take about a minute to become active.

	![](images/SG-setup-manual/057.png)

-	Start your VNC **Viewer Client** and log in with the password you entered above.  Use your own WorkshopImage IP address (not the one in the screenshot below).

	![](images/SG-setup-manual/059.png)

-	This is the WorkshopImage desktop.  The background may be different across the screen shots.  We need to disable the screen saver to prevent a screen unlock prompt.  Go to the system menu on the desktop and de-activate screen saver.  If you delay in this step and the screen locks up you will need to kill the vncserver (`vncserver -kill :1`) and restart it (`vncserver`) in your terminal window.

	![](images/SG-setup-manual/060.png)

	![](images/SG-setup-manual/061.png)

### **STEP 9**: Set up shortcut to SQLDeveloper and import connections

-	Right click on the desktop and create a new launcher on the desktop.  Enter the Name and Command:
	- **Name:** `SqlDeveloper`
	- **Command:** `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/sqldeveloper/bin/sqldeveloper`

	![](images/SG-setup-manual/062.png)

	![](images/SG-setup-manual/063.png)

-	Select the sqldeveloper icon from the following directory: `/u01/app/oracle/product/12.2.0.1/dbhome_1/sqldeveloper/`

	![](images/SG-setup-manual/064.png)

	![](images/SG-setup-manual/065.png)

-	Double click on the desktop icon to start sqldeveloper. Select no to import connections.

	![](images/SG-setup-manual/066.png)

-	Right click on connections and select import connections.

	![](images/SG-setup-manual/067.png)

-	Browse for connections file.

	![](images/SG-setup-manual/068.png)

	![](images/SG-setup-manual/069.png)

	![](images/SG-setup-manual/070.png)

	![](images/SG-setup-manual/071.png)

### **STEP 10**: Import data

-	Update the tnsnames.ora file to add pdb1 and new_pdb (used later).  Open a new terminal window and enter the following:
```
gedit /u01/app/oracle/product/12.2.0.1/dbhome_1/network/admin/tnsnames.ora
```

![](images/SG-setup-manual/072.png)

-	Copy the CDB connection entry into two more entries that we'll modify for pdb1 and new_pdb.  Update as follows.  Then save.

	![](images/SG-setup-manual/073.png)

-	Open a terminal window and enter the following:
```
. oraenv (enter ORCL when prompted)
./install_oci.sh
```

![](images/SG-setup-manual/074.png)