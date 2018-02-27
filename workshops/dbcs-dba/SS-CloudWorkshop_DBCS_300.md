![](images/SS-300/001.png)

Update: January 26, 2018

## Introduction

While the Oracle Cloud has greatly simplified DBA tasks, the DBA still has a role to play in the development and maintenance of DBCA instances.  This lab covers a few of the common DBA activities in a cloud environment.

This lab supports the following use cases:
-	Rapid creation and scaling of cloud databases.
-	Maintenance of security access.

## Objectives

-   Maintain security access.
-   Scale up an instance.
-   Add a SSH Key.
-   Recover a dropped table.

## Required Artifacts

-   The following lab does not require set up or artifacts from the previous labs.

## Maintain Security Access

Once you have a running database you may wish to open (or close) various ports.  We will create a new rule to open 1522 (not used..this is just an example).  Note: if you ran the step steps for this workshop (rather than someone else on your behalf) these steps are the same that were done to open vnc port 5901, and you can skip this activity/task.

### **STEP 1**: Create Security Rule

-   Open a browser on the WorkshopImage desktop and log into the cloud console.  Select Database (OCI).

	![](images/SS-300/002.png)
 
	![](images/SS-300/003.png)

-	In the top menu select Networking - Virtual Cloud Networks.

	![](images/SS-300/004.png)

-	Ensure you have the appropriate Compartment (Demo in this case).

	![](images/SS-300/005.png)

-	In Lab 100 you would have created a VCN already, and there should also be one from the setup steps.  Select either one.

	![](images/SS-300/006.png)

-	Select Security Lists on the left, and then select the Default Security List.

	![](images/SS-300/007.png)

	![](images/SS-300/008.png)

-	Select Edit All Rules.

	![](images/SS-300/009.png)

-	Scroll down and click on `Add Rule`.  Then enter a new 1522 rule.

	![](images/SS-300/010.png)

-	Scroll down further to `Save Securitiy List Rules`.  The rule only takes a few seconds to become active.

	![](images/SS-300/011.png)

	![](images/SS-300/012.png)

## Scale Up an Instance

Databases typically grow and require additional storage and possibly compute resources.  This shows the elastic nature of the Oracle Cloud.

### **STEP 2**: Scale Up An Instance

-   Navigate back to the DB Systems screen and select the Alpha01A-DBCS instance.

	![](images/SS-300/013.png)

-   Select `Scale Storage Up`.

	![](images/SS-300/014.png)

-   We can scale the storage to 32 Terebytes.  In this case we will add an additional 256G storage.

	![](images/SS-300/015.png)

	![](images/SS-300/016.png)

-   Note the instance will go into Service Maintenance mode and show `Updating` while the scale operation is processing.  This will only take a few minutes.

	![](images/SS-300/017.png)

-   After a few minutes refresh the screen - you should see the storage change from 256GB to 512GB.

 	![](images/SS-300/020.png)

## Add SSH Key

SSHs are required when creating a new DBCS instance.  Later you can add additional keys (eg: if you lost your existing private key) through the database console.

### **STEP 3**: Generate New Key Pair

-   Navigate to the compute desktop and open a new terminal window.  Enter the following.
    - `ssh-keygen`
    - **Enter filename:** `lab300`
    - **Then hit enter twice for no password**

 	![](images/SS-300/018.png)

-   Change private key permissons.  Enter the following.
    - `ls` -- review files - see the new public and private keys.
    - `chmod 600 lab300`

 	![](images/SS-300/019.png)

### **STEP 4**: Add SSH Key

-   Navigate back to the DB Systems page, select Alpha01A-DBCS, and select `Add SSH Keys`.

 	![](images/SS-300/021.png)

	![](images/SS-300/022.png)

-	In a terminal window enter the flowing.
	- `cat lab200.pub`

	![](images/SS-300/023.png)

-	Select the key text (carefully) and right click to select copy (or use the menu - right click more likely to work).

	![](images/SS-300/024.png)

-   Paste the key into the `Add SSH Keys` window.

	![](images/SS-300/025.png)

-   The instance will briefly go into Service Mainenance Mode and then in a few seconds after you refresh the screen you will see the instance back in normal operation.

### **STEP 5**: Confirm Access

-   Go back to your terminal window and SSH to the image using the new key.  Enter the following.
    - `ssh -i lab300 oracle@<Alpha01A-DBCS IP>`
    - `exit`

	![](images/SS-300/026.png)

## Configure RMAN for Cloud Backup, Backup the Database, and Restore a Dropped Table

### **STEP 6**: Configure Object Storage

-	Select `Storage` - `Object Storage` in the menu.

 	![](images/SS-300/027.png)

-	Create a new Bucket called `alpha` in the Demo Compartment (or the Compartment you have been assigned).

 	![](images/SS-300/028.png)

 	![](images/SS-300/029.png)

-	Create a new user for the purposes of storing the backups.  Go to `Identity` menu and select `Users`.

 	![](images/SS-300/030.png)

-	Create User called `demo.user`.

 	![](images/SS-300/031.png)

 	![](images/SS-300/032.png)
	

