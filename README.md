# Born2beRoot

[Score: 125/100](https://img.shields.io/badge/Score-125%2F100-brightgreen?style=for-the-badge)
[Language: Shell](https://img.shields.io/badge/Language-Shell-4E9A06?style=for-the-badge&logo=gnu-bash&logoColor=white)
[Platform: Debian](https://img.shields.io/badge/Platform-Debian-A81D33?style=for-the-badge&logo=debian&logoColor=white)

*A foundational project in system administration and virtualization.*

This project involves setting up a strictly monitored **Debian Server** virtual machine, implementing security protocols, user management, and automated monitoring scripts.

---

## Table of Contents

* [About](#about)
* * [System Requirements](#system-requirements)
  * * [Security Implementation](#security-implementation)
    * * [User and Group Management](#user-and-group-management)
      * * [Storage and Partitions](#storage-and-partitions)
        * * [Network and Services](#network-and-services)
          * * [Monitoring Script](#monitoring-script)
            * * [Bonus Features](#bonus-features)
              * * [Technical Details](#technical-details)
                * * [Author](#author)
                 
                  * ---
                 
                  * ## About
                 
                  * **Born2beRoot** is a 42 School project that introduces students to the world of virtualization and system administration. The goal is to install and configure a complete server environment while following strict security rules.
                 
                  * ---
                 
                  * ## System Requirements
                 
                  * * **Operating System:** Debian 11 (Bullseye) / Rocky Linux
                    * * **Virtualization:** VirtualBox
                      * * **Partitioning:** LVM (Logical Volume Management)
                        * * **Security:** AppArmor, Sudo configuration, SSH
                          * * **Firewall:** UFW (Uncomplicated Firewall)
                            * * **Password Policy:** Custom rules for password complexity and expiration
                             
                              * ---
                             
                              * ## Security Implementation
                             
                              * ### SSH Configuration
                              * * Port changed from default 22 to **4242**
                                * * Root login disabled for security
                                  * * Public key authentication encouraged
                                   
                                    * ### UFW Firewall
                                    * * Only port 4242 is open for SSH
                                      * * All other incoming connections are blocked by default
                                       
                                        * ### Password Policy
                                        * * Passwords expire every 30 days
                                          * * Minimum of 2 days before changing a password
                                            * * Warning message 7 days before expiration
                                              * * Passwords must have at least 7 characters
                                                * * Must contain uppercase, lowercase, and numbers
                                                 
                                                  * ---
                                                 
                                                  * ## User and Group Management
                                                 
                                                  * * **Sudo:** Users must be part of the `sudo` group to perform administrative tasks.
                                                    * * **Strict Logging:** Every sudo command is logged in `/var/log/sudo/`.
                                                      * * **Custom Groups:** Implementation of specific groups like `user42` for project requirements.
                                                       
                                                        * ---
                                                       
                                                        * ## Storage and Partitions
                                                       
                                                        * The system uses **LVM** for flexible storage management. The partition scheme includes:
                                                        * * `/` (root)
                                                          * * `/home`
                                                            * * `/var`
                                                              * * `/tmp`
                                                                * * `/srv`
                                                                  * * `/var/log`
                                                                   
                                                                    * ---
                                                                   
                                                                    * ## Monitoring Script
                                                                   
                                                                    * A Bash script (`monitoring.sh`) runs every 10 minutes to display system information across all terminals:
                                                                    * * Architecture and kernel version
                                                                      * * Number of physical and virtual processors
                                                                        * * Current RAM and Disk usage
                                                                          * * CPU load
                                                                            * * Last reboot date and time
                                                                              * * LVM status (active/inactive)
                                                                                * * Number of active TCP connections
                                                                                  * * Number of users logged in
                                                                                    * * Network IP and MAC address
                                                                                      * * Number of commands executed with sudo
                                                                                       
                                                                                        * ---
                                                                                       
                                                                                        * ## Bonus Features
                                                                                       
                                                                                        * * **Lighttpd:** A secure and lightweight web server.
                                                                                          * * **MariaDB:** Relational database management system.
                                                                                            * * **PHP:** Server-side scripting for dynamic content.
                                                                                              * * **WordPress:** Fully functional blog/site hosted on the VM.
                                                                                               
                                                                                                * ---
                                                                                               
                                                                                                * ## Technical Details
                                                                                               
                                                                                                * * **Hypervisor:** VirtualBox
                                                                                                  * * **Kernel:** Linux 5.10+
                                                                                                    * * **Shell:** Bash
                                                                                                      * * **Automation:** Cron jobs for the monitoring script
                                                                                                       
                                                                                                        * ---
                                                                                                       
                                                                                                        * ## Author
                                                                                                       
                                                                                                        * **Adil Bourji** -- [@adi7-x](https://github.com/adi7-x)
                                                                                                       
                                                                                                        * [GitHub](https://img.shields.io/badge/GitHub-adi7--x-181717?style=flat-square&logo=github)
                                                                                                        * [LinkedIn](https://img.shields.io/badge/LinkedIn-Adil_Bourji-0A66C2?style=flat-square&logo=linkedin)
                                                                                                       
                                                                                                        * *42 School . Common Core . System Administration*
                                                                                                        * 
