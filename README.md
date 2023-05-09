
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

Add these lines in the file:

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

<br>

### Setup App

`curl https://deb.nodesource.com/setup_19.x | sudo -E bash -` - Download node

`sudo apt-get install nodejs` - Install node

`sudo chown -R $USER:$USER /var/www` - Change owenership of the parent directory 

`mkdir /var/www/app` - Create application directory -> Create a node app here

<br>

### Connect Nginx with Node app

`sudo vi /etc/nginx/sites-enabled/<domain-name/app-name>` - Create nginx configuration file for our site

Add these lines in the file: 
     
     server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;
        index index.html;

        server_name <domain-name>;

        location / {
                proxy_pass http://127.0.0.1:3000/;
        }
     }
     
 `sudo nginx -t` - Check nginx configuration files
 
 `sudo vi /etc/nginx/nginx.conf` - Open nginx config file
 
 Change this line: 
 
     include /etc/nginx/sites-enabled/*;
   
 For: 
 
     include /etc/nginx/sites-enabled/<domain-name/app-name>;
     
 `sudo service nginx restart` - Restart nginx service 
     
 `node /var/www/app/app.js` - Run node application -> Check on the browser
 
 <br>
 
 ### Setup pm2
 
 `sudo npm i -g pm2` - Install pm2
 
 `pm2 start app.js --watch` Start pm2 and watch for changes
 
 `pm2 list` - See current pm2 current processes 
 
 `pm2 save` - Save current processes list
 
 `pm2 startup` - Return the script to setup pm2 on server start 
 
 <br>
 
 ### Setup Git
 
 [Add ssh key to Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
 
 [Add remote repo](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories)
 
 `vi ~/.ssh/config` - Open ssh config to add our key (gh_key)
 
 Add these lines to the file:
 
     Host github.com
       Hostname github.com
       IdentityFile ~/.ssh/gh_key
 
 















