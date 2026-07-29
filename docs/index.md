# Welcome to AUDITOR-demo

This is a mini tutorial on how to install an AUDITOR accounting pipeline from scratch using rpms.
The pipeline consists of an HTCondor collector, an AUDITOR instance with a PostgreSQL database and an APEL plugin. 
All components can be installed together on a small VM. The demo here was set up on a VM with 1 vCore, 2GB RAM and 20 GB disc space on an Alma 9.5 OS.  



    +--------------------+        +-------------+        +-------------+
    |HTCondor  Collector | -----> |   AUDITOR   | <----- | APEL Plugin |
    +--------------------+        +-------------+        +-------------+
                                         |
                                         v
                                  +-------------+
                                  | PostgreSQL  |
                                  +-------------+


## Tutorial Overview

1. Setup prerequisites
2. Configure the database
3. Install AUDITOR
4. Start the services
5. Configure collectors
6. Run plugins
7. Troubleshooting
