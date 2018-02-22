![](images/SS-100/001.png)

Update January 25, 2018

## Introduction

This is the first of several labs that are part of the Oracle Public Cloud Database Cloud Service workshop. These labs will give you a basic understanding of the Oracle Database Cloud Service and many of the capabilities around administration and database development.  Note that this workshop uses Oracle's Oracle Compute Infrastructure (OCI) Database VM service, and not the legacy 'Classic' service or Bare Metal service.

This lab will walk you through creating a new Database Cloud Service, and then have you connect into the Database image using the ssh private key to familiarize yourself with the image layout. Next you will learn how to create a ssh tunnel to access ports that are closed on a remote OPC instance. Using the tunnels, you will learn how to access various Database consoles.  Note that all Oracle Cloud Databases are configured by default with Transparent Data Encryption (TDE).  To migrate pluggable databases from an on-premise database that was backed up to the cloud it must be configured with archivelog and TDE prior to backup.  Since this simulated on-premise instance is in fact a DBCS instance it already has this configured, but we will need to manage password wallets to perform various migration activities.  

Note that if you performed the setup steps for this and the following labs some of these steps will be familiar.

- To log issues and view the Lab Guide source, go to the [github oracle](https://github.com/oracle/learning-library/tree/master/workshops/dbcs-dba-oci) repository.

## Objectives
-	Familiarize yourself with the Oracle Cloud Infrastructure.
-	Backup your local database to the Oracle Cloud.
-	Create a Virtual Cloud Network (VCN).
-	Create Database Cloud Service.
-   SSH configuration.
-   Explore VM and consoles.

## Required Artifacts

-   The following lab requires a VNC Viewer to connect to an Image running on Oracle's IaaS Compute Service.

## Log into the Oracle Cloud Infrastructure Console

### **STEP 1**:  Log into your Oracle Cloud Account

![](images/SS-100/002.png)

### **STEP 2**:  Access Database Console and Copy Public IP for WorkshopImage

-	You will be accessing the **Database (OCI)** service.  If it is not already visible select `Customize Dashboard`.

	![](images/SS-100/003.png)

-	Scroll down and select `Show` for `Database (OCI)`.  Do **not** select just `Database` (the one above).  Then close the window.

	![](images/SS-100/004.png)

-	Select **Database (OCI)** Service and then Console.

	![](images/SS-100/005.png)

	![](images/SS-100/006.png)	

-	Then select Database - DB Systems.

	![](images/SS-100/007.png)	

-	Note the **Public IP Address** on the WorkshopImage.  Write this down.  We will be using it several times throughout the labs.

	![](images/SS-100/008.png)	

### **STEP 3**:  Connect to the WorkshopImage using your VNC Viewer

-	If you do not already have a VNC Viewer, download realvnc or tightvnc from the internet and install on your computer. Run the VNC Viewer and enter the Public IP you just copied, along with appending :1. You will be prompted for a password.  If you ran the setup instructions for vnc server you would have entered this password yourself.  Obtain this from the person who ran the setup for you if it was another person.

	![](images/SS-100/009.png)	

-	You will log into the following desktop.

	![](images/SS-100/010.png)	

## Create Database Cloud Service

### **STEP 4**:  Create a Virtual Cloud Network (VCN)

-	Open the browser on the image desktop.  Note that while you can use your own browser on your own desktop, in step 5 below you will need a public key, and to keep things simple we'll be using the same key that was used to create the WorkshopImage, and so you need to be running the browser inside the image to select the key (below).

	![](images/SS-100/011.png)	

-	Log into your cloud account.

	![](images/SS-100/012.png)	

-	Go to Networking - Virtual Cloud Networks.  Note that if you previously set up a VCN as part of the setup process you can use that VNC instead of creating another in this step.

	![](images/SS-100/013.png)	

-	Select Compartment `Demo`.

	![](images/SS-100/014.png)

-	Next select `Create Virtual Cloud Network`.  Those that previously ran the setup steps may choose to use the existing VCN.

	![](images/SS-100/015.png)

-	Take defaults for all the fields except ensure you select `CREATE VIRTUAL CLOUD NETWORK PLUS RELATED RESOURCES` option.

	![](images/SS-100/016.png)

-	Scroll to the bottom and save and close.

	![](images/SS-100/017.png)

	![](images/SS-100/018.png)

### **STEP 5**:  Create a Database Cloud Service

-	Select Database - DB Systems.

	![](images/SS-100/019.png)

-	Select Launch DB System

	![](images/SS-100/020.png)

-	Enter the follwing details:
	- **Display Name:**  `Alpha01A-DBCS`
	- **Availability Zone:**  Select what is available.  Note depending on capacity you may need to try more than one zone.  If you do not have capacity then you will be advised when you save the configuration, after which you can try another zone.
	- **Shape Type:**  `VM.Standard1.1`
	- **Software Edition:**  `Enterprise Edition` (note if you select Standard Edition you will not be able to migrate transportable tablespaces in lab 200).
	- **Available Storage Size:** 256GB
	- **Licence Type:**  `License Included`

	![](images/SS-100/021.png)

-	Scroll down and select SSH public key.

	![](images/SS-100/022.png)

-	Select File System on the left and select the tmp directory

	![](images/SS-100/023.png)

-	Select publicKey.pub file.

	![](images/SS-100/024.png)

-	Select the VCN you just created.  The other VCN that was created for WorkshopImage during setup will also work.

	![](images/SS-100/025.png)

-	Select the available Client Subnet (only one should be available in the dropdown), and enter `alpha` as the HOSTNAME PREFIX.

	![](images/SS-100/026.png)

-	Enter the following database details:
	- **Database Name:** `ORCL`
	- **Database Version:** `12.2.0.1` (note it is important to select this version)
	- **PDB Name:**  `PDB1`
	- **Database Admin Password:** `ALpha2018__` (with two underscores)
	- **Do not select automatic backup**

	![](images/SS-100/027.png)

-	Launch DB System.  Note this will take approximately 70 minutes to create.

	![](images/SS-100/028.png)

## SSH Configuration and Image Exploration

### **STEP 6**:  Record the IP Address of the Database Cloud Service

-	Log back into the Cloud Console to copy the IP address of the new Alpha01A-DBCS instance.

	![](images/SS-100/028.png)

-	Note the IP address of the `Alpha01A-DBCS` instance.

	![](images/SS-100/030.png)

### **STEP 7**:  Open a New SSH Connection to Browse the Database Image

-	Since we will be using a terminal window frequently we will pin the terminal window to the panel.  Open a new terminal window on the VNC desktop and enter the following.

	![](images/SS-100/031.png)

-	Open a new terminal window.

	![](images/SS-100/032.png)

-	Enter the following to connect.
	- `ssh -o StrictHostKeyChecking=no -i /tmp/privateKey opc@<your-DB-IP>`

-	Enter the following to review the image layout.
	- `sudo su - oracle`
	- `. oraenv` note there is a space between dot and ora - enter `ORCL`
	- `env | grep ORA`
	- `cd $ORACLE_HOME`
	- `ls`
	- `exit`

	![](images/SS-100/033.png)

## Access the Database Consoles

### **STEP 8**:  Access Database Monitor

-	Log into the Cloud Console.  We will first be opening port 443 (https).

	![](images/SS-100/034.png)

-	Select `Database (OCI)`

	![](images/SS-100/035.png)

	![](images/SS-100/036.png)

-	Go Virtual Cloud Network

	![](images/SS-100/037.png)

-	Select the network you created and used to access Alpha01A-DBCS.  This would likely be the latest one at the top.

	![](images/SS-100/038.png)

-	Select Security Lists

	![](images/SS-100/039.png)

	![](images/SS-100/040.png)

-	Edit All Rules

	![](images/SS-100/041.png)

-	Add Rule

	![](images/SS-100/042.png)

-	Create 443 rule, and then scroll down and Save Security List Rules.

	![](images/SS-100/043.png)

	![](images/SS-100/044.png)






### **STEP 9**:  Access Enterprise Manager DB Express

-	We will use tunneling to access the EM Express Console on Alpha01A-DBCS, which is running on port 5500.  Since we are doing this from the WorkshopImage, and that image is also running EM Express on 5500, we will tunnel using 5555 (an arbitrary open port).  Open a terminal window and enter the following.  Note - do **NOT** close the window after the tunnel is opened.
	- `ssh -o StrictHostKeyChecking=no -i /tmp/privateKey -L 5555:<Alpha01A-DBCS IP>:5500 opc@<Alpha01A-DBCS IP>`

	![](images/SS-100/044.png)

### **STEP 10**:  Access Apex Monitor

