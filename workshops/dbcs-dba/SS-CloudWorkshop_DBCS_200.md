![](images/SS-200/001.png)

Update February 23, 2018

## Introduction

In this lab, you will explore some common use cases for moving your data from on-premises to the cloud. There are multiple options for solving this data movement challenge. In this lab, we will use SQL\*Developer and command line tools to clone and move a pluggable database from your on-premises database (your Virtual Machine) to your cloud database. You will also use standard Oracle Data Pump tools to export a schema from the on-premises database, and then import that data to your cloud database in a new schema. The final exercise uses RMAN to move a tablespace to the cloud.  These are only a few of the many options for moving data into and between Oracle databases.  More choices for how to migrate Oracle Databases from on-premise to the cloud can be found in this [Migration Strategies Document](http://www.oracle.com/technetwork/database/database-appliance/documentation/oda-migration-strategies-1676870.pdf).

This lab supports the following use cases:
-	Migration of on-premise pluggable databases to a cloud based environment.
-	Rapid creation of test or development pluggable database copies in the Cloud.
-	Rapid replication of on-premise data for reporting and analytics.
-	Rapid re-fresh of selected on-premise schemas for test and/or development activities and/or reporting and analytics.

- To log issues and view the Lab Guide source, go to the [github oracle](https://github.com/oracle/learning-library/tree/master/workshops/dbcs) repository.

## Objectives

-   Migrate a pluggable database from on-premise to the Cloud.
-   Migrate a schema using Oracle Data Pump.
-   Migrate data using a Transportable Tablespace.
-   Copy data using Database Links.

## Required Artifacts

-   The following labs assume that the steps outlined in lab guide 100 have been completed.
-   The SSH tunnels must be active in a terminal window.

# Cloud Migration Using Pluggable Databases

## Configure the Environment

### **STEP 1**:  Open a **DBA Navigator** connection to the on-premise database

-   From the VNC Session desktop, locate and double-click on the **SQL Developer** icon. ***NOTE***: The first time SQL Developer is brought up; it may take some time to instantiate.

	![](images/SS-200/002.png)

-   Select the **View** -> **DBA** menu option from the top dropdown menu.

	![](images/SS-200/003.png)

-   On the DBA tab, click the green plus icon to create a new connection.

	**Note**: you may also right-click on Connections and select Add Connection.

	![](images/SS-200/004.png)

-   Select the **Local Sys CDB** connection and click **OK**. 

	**Note: Local Sys CDB** is our simulated “on-premises” database in the WorkshopImage Instance.  You will be prompted for a password - use `Alpha2018_`.

	![](images/SS-200/005.png)

-   Expand the **Local Sys CDB connect**, and then expand the **Container Database** tree item. Click on the **PDB1** pluggable database to show the details for the pluggable database.

	![](images/SS-200/006.png)

## Clone the On-premise PDB1

### **STEP 2**:  Clone the PDB1

-   In the DBA Navigator panel, right click on the PDB1 pluggable database and select the Clone PDB... menu option.

	![](images/SS-200/007.png)

-	Enter the following
	- **Database Name:** `NEW_PDB`
	- Then select the SQL table above.  Since we have implemented TDE (Transparent Data Encryption) we need to override the SQL.

	![](images/SS-200/008.png)

-	Select the SQL tab above and enter the following
	- `keystore identified by ALpha2018__`

	![](images/SS-200/009.png)

	![](images/SS-200/010.png)

-	Click on the NEW\_PDB database in the DBA navigator to see the status of the database.  Note: The cloned database shows an OPEN_MODE of MOUNTED indicating the database is plugged-in but is not open for access.

	![](images/SS-200/011.png)

-	Click on the Data Files tab for the ALPHACLONE to review the data files created during the cloning operation.  Note that DBCS on OCI is using Automatic Storage Management (ASM).  

	![](images/SS-200/012.png)

## Clone the NEW_PDB DB to the Cloud

### **STEP 3**:  Change Permissions to the TDE Wallet directory and update the sqlnet.ora file.

All Oracle DBCS Services are protected by Transparent Data Encryption (TDE) by default.  Wallets with encrypted keys are used to encrypt and decrypt the data.  Any time you move the dbf data files from one instance to another you need to migrate the keys with the data.  A wallet directory holds the keys to the data, and the permissions need to modified to export or import the keys.  Enter the following in a terminal window.  The wallets are in a directory that matches your database unique name.  This was used in the Local Sys CDB connection (right click on the connection in SQL Developer), and is also in the Cloud Console (WorkshopImage - NOT Alpha01A-DBCS).

-	Enter the following in a terminal window.
	- `chmod 755 /opt/oracle/dcs/commonstore/wallets/tde/<your database unique name for WorkshopImage>`

	![](images/SS-200/013.png)

-	Update the sqlnet.ora file.  First review the file.  It specifies the wallet directory location.  This needs to be updated to replace the `$ORACLE_UNQNAME` variable with your database unique name (literal rather than variable).
	- ` cat /u01/app/oracle/product/12.2.0.1/dbhome_1/network/admin/sqlnet.ora`

	![](images/SS-200/013.1.png)

-	Update it with the following command.  Be sure to specify the name of the database noted earlier (you should have written this down).  You are replacing $ORACLE_UNQNAME with your own ORCL_iad... name.
	- `sed -i 's/$ORACLE_UNQNAME/<your database unique name for WorkshopImage>/g' /u01/app/oracle/product/12.2.0.1/dbhome_1/network/admin/sqlnet.ora`

	![](images/SS-200/013.2.png)

### **STEP 4**:  Export Encryption Keys from NEW_PDB

All DBCS instances have both an auto open wallet and a password wallet.  When moving data the **password wallet** must move with the data.  To open the password wallet you must first remove the auto open wallet.

-	Review status of encryption wallet.  Open a terminal window (or use your existing open window) and enter the following.
	- `. oraenv` (enter ORCL when prompted).
	- `sqlplus sys/ALpha2018__ as sysdba`
	- `select * from v$encryption_wallet;`

	![](images/SS-200/014.png)

-	Shut down the database, remove the auto-open wallet, and start the database back up again.  We will be using the password wallet.  Enter the following commands in the open terminal window.
	- `shutdown immediate;`
	- `exit`
	- `mv  /opt/oracle/dcs/commonstore/wallets/tde/<your database unique name for WorkshopImage>/cwallet.sso /opt/oracle/dcs/commonstore/wallets/tde/cwallet.sso`
	- `sqlplus sys/ALpha2018__ as sysdba`
	- `startup`
	- `alter pluggable database all open;`

	![](images/SS-200/015.png)

	![](images/SS-200/016.png)

-	Review the status of the **password wallet**.  Enter the following.
	- `select * from v$encryption_wallet;`

	![](images/SS-200/017.png)

-	Note the password wallet is closed in the CDB.  We need to open the password keystore in the CDB, and also in the PDB(s).  Now that the auto open wallet has been removed opening and closing wallets and keystores is a manual process.
	- `administer key management set keystore open identified by ALpha2018__ container=all;`
	- `alter session set container=new_pdb;`
	- `administer key management set keystore open force keystore identified by ALpha2018__;`

	![](images/SS-200/027.png)

-	Now we can export the encryption keys to the pluggable database.  Enter the following commands. 
	- `administer key management export encryption keys with secret "Alpha2018_" to '/home/oracle/new_pdb.p12' identified by ALpha2018__;`


	![](images/SS-200/031.png)

### **STEP 5**:  Clone NEW_PDB in WorkshopImage to Alpha01A-DBCS

-	Since we need to use tunnels to communicate with the remote DBCS instance when using ports other than 22 (which is open) we need to create a new tunnel for port 1521 back to WorkshopImage.  We first copy the SSH private key to Alpha01A-DBCS.  We then open a new terminal window and SSH into the remote Alpha01A-DBCS instance - see terminal window heading).  You will then create a tunnel in Alpha01A-DBCS back to WorkshopImage.  This might seem confusing as you are connecting to a remote instance and then creating a tunnel back to originating instance.  This will be used by the database.
	- `scp -o StrictHostKeyChecking=no -i /tmp/privateKey /tmp/privateKey opc@<Alpha01A-DBCS IP>:.`
	- `ssh -i /tmp/privateKey opc@<Alpha01A-DBCS IP>`
	- `ssh -o StrictHostKeyChecking=no -i privateKey -L 1530:<Private IP of WorkshopImage>:1521 opc@<WorkshopImage IP>`
	- Minimize this window but do not close it.

	![](images/SS-200/031.1.png)

-	Next open SQL Developer and then open the Alpha01A-DBCS connection. 

	![](images/SS-200/032.png)

-	Create a database link in Alpha01A-DBCS that points back to the WorkshopImage.  Note the database link name must match the source database and hostname.  Enter the following.

```
create database link ORCL.<host domain name>
connect to system identified by ALpha2018__
using '(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<WorkshopImage IP>)(PORT=1521))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=<database unique name>.<host domain name>)))';
```
	![](images/SS-200/034.png)

-	Test the link.  Enter the following.  Note that tunnels do tend to drop enventually over time.  If you get an error check that your tunnel is still up and in effect.
	- `select sysdate from dual@orcl.<host domain name>;`

	![](images/SS-200/035.png)

-	Reestablish tunnel if necessary.

	![](images/SS-200/036.png)

-	Clone the NEW_PDB.  Enter the following in SQL Developer.  This will take several minutes.
	- `create pluggable database new_pdb FROM new_pdb@orcl.<host domain name> keystore identified by ALpha2018__;`

	![](images/SS-200/037.png)

### **STEP 8**:  Create a SQL Developer connection to the Public Cloud database ALPHAPDB schema

-	Back in SQL Developer select the DBA view from the drop down.  We are first going to confirm the NEW_PDB exists now in the Alpha01A-DBCS instance.  

	![](images/SS-200/038.png)

-	Right click on the Connections in the DBA panel and add the Alpha01A-DBCS connection.

	![](images/SS-200/039.png)

-	Expand the Container Database branch and confirm NEW_PDB exists.  Click on it - note that it is in mounted state.

	![](images/SS-200/040.png)

-	Right click on NEW_PDB and select modify state, and then hit Apply.

	![](images/SS-200/041.png)

	![](images/SS-200/042.png)

	![](images/SS-200/043.png)

-   Now right click the Alpha01A-DBCS connection and select properties.  We will edit this to create a new connection to the NEW_PDB PDB.  window to create a new connection; enter the following connection details and click Save
	- **Connection Name**:	`Alpha01A-DBCS-NEW_PDB`
	- **Username**:			`alpha`
	- **Password**:			`ALpha2018__`
	- **Check** "Save Password"
	- **Optionally select a color for the connection**
	- **ConnectionType**:		`SSH`
	- **Port Forward:**		`Database (Alpha01A-DBCS)`
	- **Service Name**:		`new_pdb.<Your ID Domain>.oraclecloud.internal`
	
	![](images/SS-200/050.png)

-   Click **Test** to confirm the information was entered correctly.

	![](images/SS-200/051.png)
 
-   Click **Connect** to save the connection information, expand the connection on the left, and then expand the tables to confirm the migration was successful.

	![](images/SS-200/052.png)

# Cloud Migration Using Data Pump: Schema Level

## Export the Alpha Schema

### **STEP 9**:  Create directory for datapump export

-	Create an Oracle directory (may already exist).
	- `sqlplus alpha/Alpha2018_@pdb1`
	- `create directory oracle as '/home/oracle';`
	- `exit;`

	![](images/SS-200/053.png)

### **STEP 10**:  Run datapump export

-	Export the data.
	- `expdp alpha/Alpha2018_@pdb1 directory=oracle dumpfile=alphaexp.dmp compression=all`

	![](images/SS-200/054.png)

### **STEP 11**:  Copy the export Data Pump file to the server

-   Use the following secure copy (**scp**) command to transfer the Data Pump export to the Alpha01A-DBCS instance.
	- `scp -i /home/oracle/privateKey /home/oracle/alphaexp.dmp oracle@<Alpha01A-DBCS IP>:.`

	![](images/SS-200/055.png)

### **STEP 12**:  Create a new schema to hold a copy of the data

-	Open a new terminal window (or use the current one) and enter the following commands that will SSH to the Alpha01A-DBCS instance, create an Oracle directory, and import the data.
	- `ssh -i /home/oracle/privateKey oracle@<Alpha01A-DBCS IP>` -- log into your remote DBCS instance
	- `sqlplus system/Alpha2018_@pdb1;` -- log into system in the alphapdb
	- `create user alpha2 identified by Alpha2018_;` -- create schema alpha2 (alpha already has the data from the previous lab)
	- `grant dba to alpha2;`
	- `connect alpha2/Alpha2018_@pdb1;` -- connect to alpha2 so we can create the oracle directory
	- `create directory oracle as '/home/oracle';` -- this may already exist
	- `exit`

	![](images/SS-200/056.png)

### **STEP 13**:  Import the data

-	Import the data from the alpha schema to the new alpha2 schema.  Run the following command in your terminal window.
	- `impdp alpha2/Alpha2018_@pdb1 directory=oracle dumpfile=alphaexp.dmp remap_schema=alpha:alpha2`

	![](images/SS-200/057.png)

# Cloud Migration Using Data Pump: Tablespace Level

While datapump provides a very fast multi-threaded technique to move data quickly between Oracle Databases, it was not designed for very large volumes of data.  Transportable Tablespaces enable a database file copy technique that does not require an exported copy of the data, but instead allow you to copy the in-place data files to target, which using Datapump to capture the metadata only.

## Replicate Current Data to Archive Tablespace

We will be creating a copy of the alpha schema and tablespace, and replicating that to a alpha_archive schema and tablespace.

-	Open a terminal window and execute the following:
	- `/home/oracle/cr_tablespace.sh`

	![](images/SS-200/058.png)

### **STEP 14**:  Open alpha_archive tablespace in read only mode and export the metadata

-	Log into the database, put the alpha_archive tablespace in in read only mode, and export the tablespace metadata.
	- `sqlplus system/Alpha2018_@pdb1`
	- `alter tablespace alpha_archive read only;`
	- `exit`
	- `expdp system/Alpha2018_@pdb1 directory=oracle dumpfile=alpha_archive_tbs.dmp transport_tablespaces=alpha_archive exclude=statistics encryption_password=Alpha2018_ logfile=full_tts_export.log`

	![](images/SS-200/059.png)

### **STEP 15**:  Copy the datafiles to the target DBCS instance

-	In the terminal window enter the following:
	- `scp -i /home/oracle/privateKey /home/oracle/alpha_archive_tbs.dmp oracle@<Alpha01A-DBCS IP>:.` -- metadata
	- `scp -i /home/oracle/privateKey /u02/app/oracle/oradata/ORCL/PDB1/alpha_archive.dbf oracle@<Alpha01A-DBCS IP>:.` -- datafiles

	![](images/SS-200/060.png)

### **STEP 16**:  Import the tablespace into the target DBCS instance

We will be importing the data into the pdb1 instance.  We need to first create the alpha_archive user that owns the data.

-	SSH to the target DBCS instance, log in, and create euro user (must already exist in the target).
	- `ssh -i /home/oracle/privateKey oracle@<Alpha01A-DBCS IP>`
	- `sqlplus system/Alpha2018_@pdb1`
	- `create user alpha_archive identified by Alpha2018_;`
	- `grant dba to alpha_archive;`
	- `exit`

	![](images/SS-200/061.png)

-	Copy the alpha_archive.dbf datafile to the pdb1 directory and import the metadata (the data is already there in the dbf file).
	- `cp /home/oracle/alpha_archive.dbf /u02/app/oracle/oradata/ORCL/PDB1`
	- `impdp system/Alpha2018_@pdb1 directory=oracle dumpfile=alpha_archive_tbs.dmp logfile=full_tts_imp.log encryption_password=Alpha2018_ transport_datafiles='/u02/app/oracle/oradata/ORCL/PDB1/alpha_archive.dbf'`

	![](images/SS-200/062.png)

-	Confirm tablespace and contents exist by querying the Oracle dictionary.  Log into sqlplus and run the following query.
	- `sqlplus system/Alpha2018_@pdb1`
	- `select tablespace_name, count(*) from dba_tables where owner='ALPHA_ARCHIVE' group by tablespace_name;`
	- `exit`
- 
	![](images/SS-200/063.png)

# Cloud Migration Using Database Links (Table Level)

Occasionally you just want to copy one or more tables from one database to another, and the easiest/quickest way to do that is to use a database link.  A database link connects two databases with sqlnet allowing you to reference remote tables in your local database.  This is good for table data, but other object types such as stored procedures, etc. cannot be replicated as easily.

## Create a Database Link

### **STEP 17**:  Create Database Link on the local system

-	Since we need to use tunnels to communicate with the remote DBCS instance when using ports other than 22 (which is open) we need to create a new tunnel for port 1521.  Open a new terminal window (be sure you are not SSH into the remote Alpha01A-DBCS instance - see terminal window heading).
	- `ssh -o StrictHostKeyChecking=no -i /home/oracle/privateKey -L 1526:<Alpha01A-DBCS IP>:1521 opc@<Alpha01A-DBCS IP>`
	- Minimize this window.

	![](images/SS-200/064.png)

-	Log into SQL Plus and create the database link.  Be sure to update this command with your Identity Domain.
	- `sqlplus alpha/Alpha2018_@pdb1;`
	- `create database link alpha_dbcs connect to alpha_archive identified by Alpha2018_ using '(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=localhost)(PORT=1526))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=pdb1.gse00011358.oraclecloud.internal)))';`
	- `select sysdate from dual@alpha_dbcs;`

	![](images/SS-200/065.png)

-	Query the REMOTE alpha_archive schema and list tables.  Then copy a table from the remote DBCS to the local instance.  Note that you cannot create a remote table (DDL operation) on the remote server but you can do an insert operation.  
	- `select table_name from user_tables@alpha_dbcs;`
	- `create table alpha_services_stats_archive as select * from alpha_services_stats@alpha_dbcs;`

	![](images/SS-200/066.png)

This lab shows how you can copy entire databases, tablespace datafiles, schemas, and individual tables between on-premise databases and cloud databases.  Depending on the use case one or more of these approaches may be applicable.