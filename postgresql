1. [OPTIONAL] Install and configure PostgreSQL 9.5. If you just want to use a local disk database, you're all set. However, if you want use a local PostgreSQL database, or if you want to use this particular workstation as a PostgreSQL server for other Resolve workstations on your network, follow the additional steps below.
	1. Install the repository RPM:
		
		```$ sudo yum install yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm```
	1. Install the client packages:
		
		```$ sudo yum install postgresql95```
	1. Install the server packages:
		
		```$ sudo yum install postgresql95-server```
	1. Initialize the database and enable automatic start:
		
		```
		$ sudo /usr/pgsql-9.5/bin/postgresql95-setup initdb
		$ sudo systemctl enable postgresql-9.5
		$ sudo systemctl start postgresql-9.5
		```
	1. Set up a static IP address:
		1. Run either `$ ifconfig a` or `$ ip a`to view the name of the connected ethernet device.
		1. `vi` into `/etc/sysconfig/network-scripts/ifcfg-<yourinterfacename>`.
		1. Modify the specific parameters:
		
			```
			BOOTPROTO=none

			# Here, after the =, you can enter whatever you had previously with DHCP, since we know that the address assigned via DHCP worked
			IPADDR=<yourIPaddress>
			
			# Here's the subnet mask. Mine is 255.255.255.0, but your network might be different
			NETMASK=255.255.255.0
			
			# This one is going to depend on your particular router. Check your router
			GATEWAY=<yourgatewayIPaddress>

			# This will depend on your ISP. Since mine is Comcast, I use 75.75.75.75
			DNS1=75.75.75.75

			DEFROUTE=yes

			IPV4_FAILURE_FATAL=no

			# Disable IPv6
			IPV6INIT=no

			# Activate on Boot
			ONBOOT=yes
			```
		1. Restart the network service for these changes to take effect.
			
			```$ sudo systemctl restart network```
		1. Configure PostgreSQL for sharing by configuring the `pg_hba.conf` file:
			1. Become the `postgres` superuser:
				
				```$ sudo su - postgres```
			1. From `/var/lib/pgsql/`, `cd` into `/var/lib/pgsql/9.5/data`:
				
				```# cd 9.5/data/```
				If you check `# pwd`, you'll see that you're in `/var/lib/pgsql/9.5/data`.
			1. Make a copy of `pg_hba.conf`, just in case anything goes wrong:
				
				```# cp pg_hba.conf pg_hba.conf.backup```
			1. `vi` into `pg_hba.conf` and [modify it so as to enable sharing](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-7) by adding in this line to the very bottom of the file:
				
				```host     all     all     <your workstation's static IP>/24     md5```
		1. Modify `postgresql.conf` to allow incoming TCP/IP sockets:
			1. `vi` into `/var/lib/pgsql/9.5/data/postgresql.conf`
			1. Scroll all the way to the bottom, and add the uncommented line:
				
				```listen_addresses = '*'```
		1. Assign a default password of `DaVinci` to the `postgres` user account
			1. Enter the `psql` shell:
				
				```# psql```
			1. Create a password for the user `postgres` by entering:
				
				```\password```
			1. Enter `DaVinci`. The `psql` shell will prompt you to reenter the password to confirm that you’ve typed it correctly.
			1. You can then exit the `psql` shell by entering `\q`.
			1. You can then exit from being the `postgres` superuser and get back to your regular user account by entering `exit`.
		1. Disable and stop the default CentOS firewall:
		
			```
			$ sudo systemctl disable firewalld
			$ sudo systemctl stop firewalld
			```
			Reboot the system.
		1. Verify that the PostgreSQL server is running properly:
			1. Enter:
				
				```cat /etc/services | grep 5432```
			You should see:
				
				```
				postgres     5432/tcp     postgresql     # POSTGRES
				postgres     5432/udp     postgresql     # POSTGRES
				```
			This means that PostgreSQL is good to go through port 5432.
		1. You can also check:
			
			```$ netstat -tulpn | grep 5432```
			Which should show that `0.0.0.0:5432` is good to go for TCP.
		1. Enter:
			
			```$ service postgresql-9.5 status```
			Here you’ll see some information about how TCP connections are listening through port 5432, with IPv4 IP addresses:
				
				```tcp     0     0 0.0.0.0:5432     0.0.0.0:*     LISTEN```		
	1. If you're going to be performing serious work with PostgreSQL databases, make sure that you back up and optimize the databases regularly, which can be done [automatically](https://github.com/sethgoldin/davinci-resolve-postgresql-workflow-tools).
