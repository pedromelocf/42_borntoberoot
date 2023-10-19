# Born2BeRoot

# Documentation

Sources:

https://www.ssh.com/academy/ssh/protocol

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

Create a partition from hardisk;

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

1. Configurações de Senha

    Acesse o arquivo de configuração

    `nano /etc/login.defs`

    Altere a linha 160: tempo em dias para a senha expirar

    `PASS_MAX_DAYS   30`

    Altere a linha 161: tempo em dias que a senha pode ser alterada antes de expirar

    `PASS_MIN_DAYS   2`

    Altere a linha 162: tempo em dias para a mensagem de alerta antes da senha expirar

    `PASS_WARN_AGE   7`

2. Política de Senhas Forte - Instalação do lib-pwquality

    `apt install libpam-pwquality`

    Verifique a instalação:

    `dpkg -l | grep libpam-pwquality`

    Acesse o arquivo de configuração

    `nano /etc/pam.d/common-password`

    Linha 25: acrescentar as regras:

    `password	requisite	pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root`

    | Política | Descrição |
    | --- | --- |
    | retry=3 | número máximo de tentativas |
    | minlen=10 | tamanho mínimo de caracteres da senha |
    | ucredit=-1 | pelo menos uma letra maiúscula |
    | dcredit=-1 | pelo menos um número |
    | maxrepeat=3 | não pode ter mais que 3 caracteres idênticos consecutivos |
    | reject_username | não pode incluir o nome do usuário |
    | difok=7 | não pode repetir 7 dos caracteres da senha anterior |
    | enforce_for_root | o root deve obedecer os mesmos critérios |

- [ ]  Cron/Script

Create the rule named [monitoring.sh](http://monitoring.sh) at usr/local/bin directory showing the specific information:

| $ARCH | The architecture of your operating system and its kernel version. |  |
| --- | --- | --- |
| $CPU | The number of physical processors. |  |
| $VCPU | The number of virtual processors. |  |
| $RAM | The current available RAM on your server and its utilization rate as a percentage |  |
| $DISK | The current available memory on your server and its utilization rate as a percentage |  |
| $CPULOAD | The current utilization rate of your processors as a percentage. |  |
| $LASTBOOT | The date and time of the last reboot. |  |
| $LVMUSE | Whether LVM is active or not |  |
| $CONECT | The number of active connections |  |
| $USERLOG | The number of users using the server |  |
| $NETWORK | The IPv4 address of your server and its MAC (Media Access Control) address. |  |
| $SUDO | The number of commands executed with the sudo program. |  |

Open crontab and add the rule:

```
$ sudo crontab -u root -e
```

Add at end as follows: (*/10 means every 10 mins the script will show. It will only run the script in specific times. Ex: 10:00 am; 10:10 am, 10:20 am. It will ignore the server startup time. Thats why we need to create a function to sleep based on server startup. Try it! ).

```
*/10 * * * * /usr/local/bin/monitoring.sh
```

- [ ]  Install and set wordpress
- [ ]  Install and set MariaDB
- [ ]  Install extra feature
