# 42_born2beroot

<div align="center">

This project aims to introduce you to the wonderful world of virtualization.

Skills: Network & system administration;

<img src="https://github.com/pedromelocf/utilities/blob/master/born2berootm.png" />

![](https://komarev.com/ghpvc/?username=pedromelocf)

<h3> Check my article about this project: </h3> 
https://pedromelodev.com/index.php/2023/11/14/born2beroot-virtual-machines-debian-secure-server-mastery/

<h2> Grade </h2>

<img src="https://github.com/pedromelocf/utilities/blob/master/125.png" />

</div>

<h2> Instructions </h2>

Sources:

https://www.ssh.com/academy/ssh/protocol

https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-debian-10-pt

Steps:

- [x]  Download .ISO from [https://debian.org](http://debian.org)
- [x]  Open VirtualBox
- [x]  Create VM and SO Install

Definir linguagem, idioma e localização, configurações de teclado;

Definir hostname;

Let domain name blank;

Set root password (Write it!);

Set up user account with your login and password;

Set partition method to manual;

Create a partition from hardisk for boot;

Encrypt partition with default configuration and set password;

Configure LVM and create volume group named LVM-Group using encrypted partition;

Create logical volume name as pdf;

Configure logical volume following specification;

Set location for updating packages and left blank;

No popularity contest;

Unselect all and install only the basic;

Install grub boot loader;

- [x]  Install and configure Sudo

1. Login as root: $su
2. $ apt install sudo

 3.  $ sudo usermod -a -G groupname username

 4. $ login username

- [x]  Install SSH and configure

`$ sudo apt install openssh-server`

`$ cd etc/ssh`

`$ sudo nano sshd_config`

1. Remove port comment and insert 4242 port as available
2. Remove sudo login comment and change it to “no”

`$ sudo service ssh status`

`$ ip addr | grep inet`

- [x]  Install UFW Firewall and configure

`$ sudo apt install ufw`

`$ sudo ufw enable`

`$ sudo ufw allow 4242`

`$ sudo service ufw status`

- [x]  Create user and groups

Show user groups: `$ groups user`

New user: `$ sudo useradd user`

Set password: `$ sudo passwd user`

Create group: `$ sudo groupadd group`

Show groups :

 1.  `$ cd ./etc`

1. `$ cat group`

Insert user into a group: `$ sudo usermod -a -G groupname username`

- [x]  User managment

1. Create a directory where sudo log commands will be saved

    `$ sudo mkdir /var/log/sudo`

2. Open sudo config file

    `$sudo visudo`

    Insert into file:

    Password tries for 3:

    `Defaults	passwd_tries = 3`

    Return message error when password is wrong:

    `Defaults	badpass_message = "Wrong Password, bro! Don´t you forgot it, right?"`

    Save all log sudo acess:

    `Defaults	logfile="/var/log/sudo/sudo.log"`

    `Defaults	log_input, log_output`

    `Defaults	iolog_dir="/var/log/sudo"`

    Set TTY (Print the terminal filename conected to the standard input)

    `Defaults	requiretty`

    Set sudo local for only root and sudo users:

    `Defaults	secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"`


- [x]  Password policy

1. Password policy

Acess file configuration

`nano /etc/login.defs`

Change 160 line: time to expire password

`PASS_MAX_DAYS   30`

Change 161 line: time before user cannot login

`PASS_MIN_DAYS   2`

Change 162 line:  Password expiration alert

`PASS_WARN_AGE   7`

1. Strong password policy

Installing lib-pwquality

`apt install libpam-pwquality`

Verify instalation:

`dpkg -l | grep libpam-pwquality`

Acess file configuration

`nano /etc/pam.d/common-password`

Append rules to line 25

`password	requisite	pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root`

| Polytics | Description |
| --- | --- |
| retry=3 | max attemps |
| minlen=10 | min lenght password |
| ucredit=-1 | at least 1 char uppercase |
| dcredit=-1 | at least 1 number |
| maxrepeat=3 | less than 3 equal chars consec |
| reject_username | reject username |
| difok=7 | cant repeat 7 chars from old password |
| enforce_for_root | include rule for root |

- [x]  Cron/Script

Create the rule named [monitoring.sh](http://monitoring.sh) at usr/local/bin directory showing the specific information:

  GNU nano 7.2                                                             /usr/local/bin/monitoring.sh

```
#!/bin/bash

ARCH=$(uname -a)
CPU=$(lscpu | grep Soquete | awk '{print $2}')
VCPU=$(nproc)
RAM1=$(free -m | grep Mem.: | awk '{print $3}')
RAM2=$(free -m | grep Mem.: | awk '{print $2}')
RAMUSAGE=$(free -m | grep Mem.: | awk '{printf "%.2f%", $3/$2*100}')
DISKUSAGE=$(df | grep root | awk '{printf "%.0f", $3/1000}')
DISKTOTAL=$(df -h | grep root | awk '{printf "%0.fGB", $2}')
DISKPERCENTAGE=$(df -h | grep root | awk '{printf "%d%", $5}')
CPUSAGE=$(mpstat | grep all | awk '{print $12}')
CPULOAD=$(awk -v cpusage="$CPUSAGE" 'BEGIN{printf "%.1f%", 100-cpusage}')
LASTBOOT=$(who -b | cut -d "a" -f 4)
LVMUSE=$(if lsblk | grep -q "lvm"; then echo "yes"; else echo "no"; fi)
CONNECTIONS=$(netstat -an | grep ESTABELECIDA | wc -l)
USERLOG=$(who | wc -l)
IPADDRS=$(ip addr | grep dynamic | cut -d "/" -f 1 | awk '{print $2}')
MACADDRS=$(ip addr | grep ether | awk {'print $2'})
SUDO=$(grep -c "COMMAND" /var/log/sudo/sudo.log)

wall "
#Architecture: $ARCH
#CPU physical: $CPU
#vCPU : $VCPU
#Memory Usage: $RAM1/${RAM2}MB ($RAMUSAGE)
#Disk Usage: $DISKUSAGE/$DISKTOTAL ($DISKPERCENTAGE)
#CPU load : $CPULOAD
#Last boot:$LASTBOOT
#LVM use: $LVMUSE
#Connections TCP : $CONNECTIONS ESTABLISHED
#User log: $USERLOG
#Network: IP $IPADDRS ($MACADDRS)
#Sudo: $SUDO cmd
"

```

Open crontab and add the rule:

```
$ sudo crontab -u root -e
```

Add at end as follows: (*/10 means every 10 mins the script will show. It will only run the script in specific times. Ex: 10:00 am; 10:10 am, 10:20 am. It will ignore the server startup time. Thats why we need to create a function to sleep based on server startup. Try it! ).

```
*/10 * * * * /usr/local/bin/sleep.sh
```

Insert script [sleep.sh](http://sleep.sh) for deal with server startup time.

```bash
#!/bin/bash

tmp=$(uptime -s | cut -d ":" -f 2)
minutes=$((tmp%10))
seconds=$(uptime -s | cut -d ":" -f 3)

sleep ${minutes}m ${seconds}s

/usr/local/bin/monitoring.sh
```

- [x]  Install lightpdd and configure

```bash
$ apt install lighttpd
$ sudo lighty-enable-mod fastcgi
$ sudo lighty-enable-mod fastcgi-php
$ sudo service lighttpd force-reload
$ sudo systemctl status lighttpdroot
```

- [x]  Install and set wordpress

```bash
$ apt install wget
```

- [x]  Install PHP

```bash
$ apt install php
$ sudo apt install php-cgi php-mysql
$ curl -s https://api.wordpress.org/secrete-key/1.1/salt
Change wordpress key in correct place
```

- [x]  Install and set MariaDB

```bash
$ apt install mariadb-server
$ sudo mariadb
$ mysql -u root
$ CREATE DATABASE <database-name>;
$ CREATE USER 'user1'@localhost IDENTIFIED BY 'password1';
$ SELECT User FROM mysql.user;
$ FLUSH PRIVILEGES;
```

- [x]  Install extra feature

```
Install GitHub CLI:
1 - Install git;
2 - Configure ssh key with github key.

```
