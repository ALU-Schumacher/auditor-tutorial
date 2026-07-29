# Database

Create and initialize the PostgreSQL database.



## Prerequisits 
### General Software 


Since the backend database of AUDITOR is postgresql, we need to have a current version installed and running.
Therefore we follow the documentation in [www.postgresql.org](https://www.postgresql.org/download/linux/redhat/)

For our setup alma9 on x86_64 we can use:
 
### Install DB
 
**Install the repository RPM:**
```
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

 
**Disable the built-in PostgreSQL module:**
```
sudo dnf -qy module disable postgresql
```

 
**Install PostgreSQL:**
```
sudo dnf install -y postgresql17-server
```

 
**Initialize the database and enable automatic start:**
```
sudo /usr/pgsql-17/bin/postgresql-17-setup initdb
sudo systemctl enable postgresql-17
sudo systemctl start postgresql-17
```
 

 
Now we need to setup the password for the postgres user (here we set it to "password"):
 
```
sudo -u postgres psql
 
psql (17.4)
 
Type "help" for help.
 

 
postgres=# \password postgres
 
Enter new password for user "postgres": 
 
Enter it again: 
 
postgres=# 
 
```
## Prepare the DataBase

The AUDITOR repository contains the required db schema. There are two valid options to inject the db schema to the postgresql db.
Either with the psql command line interface as postgres user or by using sqlx, which ist a RUST based library (the second option is described in the documentation).

### Prepare the Data Base with psql cli

Create the auditor database in postgresql:

```
psql -h localhost -U postgres
Password for user postgres: 
psql (17.4)
Type "help" for help.

postgres=# CREATE DATABASE auditor;
CREATE DATABASE
postgres=# 
```
