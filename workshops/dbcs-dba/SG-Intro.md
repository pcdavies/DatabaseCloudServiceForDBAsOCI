# Workshop Introduction

## How to Prepare for This Workshop

- To log issues and view the Lab Guide source, go to the [github oracle](https://github.com/oracle/learning-library/tree/master/workshops/dbcs-dbaoci) repository.

- The Labguides are best viewed using the Workshop's [GitHub Pages Website URL](https://oracle.github.io/learning-library/workshops/dbcs-dbaoci/) 

- Once you are viewing the Workshop's GitHub Pages website, you can see a list of Lab Guides at any time by clicking on the **Menu Icon**

![](images/WorkshopMenu.png)  

## Database Cloud Services Workshop for Oracle Compute Infrastructure (OCI)

Welcome to the Oracle Database Cloud Service Workshop. This particular version is based on Oracle's 'OCI' services, versus Oracle 'Classic' services. These labs will give you a basic understanding of the Oracle Database Cloud Service and many of the capabilities around administration and database management. Lab 100 will introduce you to DBCS and show you how to create a Cloud Database. Lab 200 will cover various data migration use cases, including pluggable database migration, tablespace migration, schema migration, and ad-hoc table copying between databases. Lab 300 covers some common Database management activities. Lab 400 show you how to use the Oracle Command Line Interface (CLI) commands to perform various tasks.

## Workshop Details

**Reference the following Lab Guides by opening their Documentation Files:**

## Lab 100: Introduction and Database Creation

**Documentation**: [SG-CloudWorkshop\_DBCS\_100.md](SG-CloudWorkshop\_DBCS\_100.md)

### Objectives

-   Configure archive logging
-	Configure Transparent Data Encryption (TDE - required to restore from backup to the cloud)
-	Backup your local database to the Oracle Cloud
-	Drop a table and then restore it from your cloud backup.
-	Create Database Cloud Service from your on-premise backup 
-   SSH configuration
-   Explore VM and consoles

## Lab 200: Data Migration

**Documentation**: [SG-CloudWorkshop\_DBCS\_200.md](SG-CloudWorkshop\_DBCS\_200.md)

### Objectives

-   Clone, unplug, transfer and plug the AlphaPDB pluggable database using SQL Developer.
-   Export and import a schema using SQL Developer to execute Oracle Data Pump jobs.
-   Export and import a small collection of tables using SQL Developer.

## Lab 300: Common DBA Tasks

**Documentation**: [SG-CloudWorkshop\_DBCS\_300.md](SG-CloudWorkshop\_DBCS\_300.md)

### Objectives

-   Create an instance.
-   Maintain security access.
-   Scale up an instance.
-   Add a SSH Key.


## Lab 400:  CLI/Curl Commands (Programatic Control of Services)

**Documentation**: [SG-CloudWorkshop\_DBCS\_400.md](SG-CloudWorkshop\_DBCS\_400.md)

### Objectives

-   Request information about DBCS related configuration and services.
-   Create services with curl commands.
