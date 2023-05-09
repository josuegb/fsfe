
# Frontend masters:  full stack frontend 

<br>

## Commands

<br>

### Login into the server

`ssh-keygen` - Generate ssh key

`ls | grep <file-name>` - Filter files by name

`cat <key-name>.pub` - See the generated public key

`ssh -i ~/.ssh/<key-name> root@<server-ip>` - Login into the server root

#### Save keys in keychain

`vi ~/.ssh/config` - Open ssh config file

Add this lines in the file:

     Host *
       AddKeysToAgent yes
       UseKeychain yes

`ssh-add --apple-use-keychain <key-name>` - Add key to the keychain (Mac OS)

`ssh root@<server-ip>` - Login into the server root with the keychain

<br>

### Update Software

`apt update` - Download new updates

`apt upgrade` - Install new updates

`shutdown now -r` Restart the server

<br>

### Create and update users

`adduser <user-name>` - Create a new user

`usermod -aG sudo <user-name>` - Add user to "sudo" group (super user group)

`su <user-name>` Switch user

`sudo cat /var/log/auth.log` - Check sudo access 

#### Enable login as new user

`mkdir ~/.ssh` - Create a *.ssh* directory if it don't exists

`touch authorized_keys` - Create *authorized_keys* file -> Copy the previously generated public key in this file

`ssh <user-name>@<server-ip/server-name>` - Login into the server with the new user using the keychain

#### Disable root login

`chmod 644 ~/.ssh/authorized_keys` - Change permission to only allow current user to write on this file

`sudo vi /etc/ssh/sshd_config` - Access to the ssh deamon process config -> Set PermitRootLogin to "no"

`sudo service sshd restart` - Restart sshd service to read config changes

<br>

### Setup Nginx

`sudo apt install nginx` - Install nginx 

`sudo serice nginx start` - Start nginx service

`cd /etc/nginx` - Go to Nginx directory 

`less sites-available/default` - Check default configuration 

`cd var/www/html` - Go the default page location

`sudo vi index.html` - Create a default html file













