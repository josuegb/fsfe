
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

`sudo cat /var/log/auth.log` - Open auth log of our server (Check sudo access)

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

`sudo vi /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe)` - Create nginx configuration file for our site

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
 
     include /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe);
     
 `sudo service nginx restart` - Restart nginx service 
     
 `node /var/www/app/app.js` - Run node application -> Check on the browser
 
 <br>
 
 ### Setup pm2
 
 `sudo npm i -g pm2` - Install pm2
 
 `pm2 start <node-server-file>(app.js) --watch` Start pm2 and watch for changes
 
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
       IdentityFile ~/.ssh/<keyname>(gh_key)
       
`chmod 600 ~/.ssh/config` - Change permissions for ssh config   

`chmod 600 ~/.ssh/<key-name>(gh_key)` - Change permissions for the ssh key (gh_key)

`git init` - Init your git repo in your app folder

`git remote add origin <url>` - Connect remote repo with local repo (use ssh url)

`git fetch origin` - Sync the the remote repo with your local repo

`git checkout main` - Change to the repo main branch

<br>

### In case you get stuck

`ssh -Tv git@github.com` - Test ssh connection with github

`:w !sudo tee %` - Save readonly file on VIM 

`pkill <process>` - Stop a running process

`stat -c %a <file>` - View permissions as numbers

<br>

### nmap

`sudo apt install nmap` - Install nmap

`nmap <server-ip>` - See open ports on the server

<br>

### ufw - Uncomplicaded firewall

`sudo ufw status` - Check firewall status

`sudo ufw allow ssh` - Allow ssh

`sudo ufw allow http` - Allow http

`sudo ufw enable` - Enable firewall

<br>

### Permissions

**R**ead    4

**W**rite   2

e**X**ecute 1

Code number:
| owner | group | everyone else |
| :----: | :----: | :----: |
| rwx  | rwx | rwx |
| 4+2+1  | 4+2+1 | 4+2+1 |
| 7  | 7 | 7 |

`chmod <code-number <file-name>` - Change file permissions

`chmod <code-number <dir>` - Change dir permissions recursively to it and its subdirectories

<br>

### Unattended Upgrades

`sudo apt install unattended-upgrades` - Install unattended upgrades

`sudo dpkg-reconfigure --priority=low unattended-upgrades` - Enable upgrades

<br>

### Create a cronjob to pull from github our last changes automatically 

`which bash` - Return the location of bash (it needs to be at the beginning of each script)

`vi <script-name>.sh` - Create a new file script (github.sh)

Add these lines to the file:

     #! <bash-location>(/usr/bin/bash)
     cd <app-dir>(/var/www/app)
     git pull origin <git-branch>(main) --ff-only
     
`chmod 700 <script-name>` - Change file permission to execute it (execute is defualt disabled) 

`./<script-name>` - Execute the script to test it

#### Cronjob

[Cron structure](https://crontab.guru/)

`crontab -e` - Edit cronjobs (if first time you have to select an editor, 2 for vim for example)

Add this line to the file:

     * * * * * sh <script-file>(/var/www/app/github.sh) 2>&1 | logger -t <log-name>(github.sh)
     
This part is for log the git command output: `2>&1 | logger -t <log-name>(github.sh)` 

`sudo tail -f /var/log/syslog` - Actively follow syslog file (check if our cronjob is working)

<br>

### Logging

Log files are located in `/var/log/`

We typically check for these files there:

- syslog
- auth.log
- nginx/access.log

#### Ways to read read log files

`tail <file>` - Output the last part of a file (you can add `-f` to the command to follow the output of the file)

`head <file>` - Output the first part of a file

`less <file>` - Output one page at time

`cat <file>` Output entire file

#### Standard Streams

- Standard output `stdout`
- Standard input `stdin`
- Standard error `stderr`

Redirection examples:

`<commmand> | <command>` - Reads from stdout 

`<commmand> > <file>`- Write stdout to file

`<commmand> >> <file>`- Append stdout to file

`<commmand> < <file>`- Read from stdin (it will read the content of the file and use it as input for the command)

`<commmand> 2>&1 <command>` - Redirect both stderr and stdout

#### Finding things

Use this structure -> `find <dir> <option> "<file/folder>"` 

`find /var/log -type f -name "*.log"` - Find all log files in /var/log

`sudo !!` - run last command with sudo privileges

`find / -type d -name log` - Find all directories with the name log

#### grep

Use this structure -> `grep <options> "<search-expression>" <dir>`

`ps aux | grep node` - Find running node processes

<br>

### Subdomain

`sudo vi /etc/nginx/sites-enabled/<subdomain-name>.<domain-name/app-name>(blog.fsfe)` - Add new subdomain (blog.fsfe)

Add these lines to the file:

     server {
	     listen 80;
	     listen [::]:80;

	     server_name <subdomain-name>(blog).<domain-name>;

	     location / {
		     proxy_pass http://localhost:3000;
	     }
     }
     
 `sudo vi /etc/nginx/nginx.conf` - Open nginx config file
 
 Add this line in the "Virtual Host Configs":
 
     include /etc/nginx/sites-enabled/<subdomain-name>(blog).<domain-name/app-name>(fsfe)
     
 `sudo nginx -t` - Check nginx configuration files
 
 `sudo service nginx restart` - Restart nginx service
 
 <br>
 
 ### Websockets
 
 `sudo vi /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe)` - Open site nginx configuration 
 
 Add these lines in the file under "location" block:
 
 	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection "upgrade";
	
`sudo nginx -t` - Check nginx configuration files
 
`sudo service nginx restart` - Restart nginx service
	
Stop our simple server and start the websocket server

`pm2 stop <node-server-file>(app.js)` - Stop process for simple server (app.js)

`pm2 start <node-server-file>(index-ws.js)` - Start process for websocket server (index-ws.js)

`pm2 save` - Save new current processes

#### Modify github script to run npm install on with the cronjob

`vi github.sh` - Open github script file

Append this line to the file:

	npm i
	
<br>

### Implementing HTTPS with Certbot

[Certbot](https://certbot.eff.org/)

`cat /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe)` - Check certbot updates in nginx configuration file

`sudo ufw allow https` - Open HTTPS port on firewall 

<br>

### Implement HTTP2 

`sudo vi /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe)` - Open nginx configuration file

 Change these lines: 
 
 	listen [::]:443 ssl ipv6only=on; # managed by Certbot
	listen 443 ssl; # managed by Certbot
   
 For: 

	listen [::]:443 http2 ssl ipv6only=on; # managed by Certbot
	listen 443 http2 ssl; # managed by Certbot
     
`sudo nginx -t` - Check nginx configuration files 

`sudo service nginx restart` - Restart nginx service 

<br>

### Creating a Docker container 

`vi Dockerfile` - Create a docker file (in /var/www/app)

Add these lines to the file:

	FROM node:19-alpine3.16
	RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app
	WORKDIR /home/node/app
	COPY --chown=node:node package*.json ./
	USER node
	RUN npm install
	COPY --chown=node:node . .
	EXPOSE 3000
	CMD ["node", "app.js"]

`sudo apt install docker.io` - Install docker with apt

`docker` - Check if docker is available 

`sudo docker build -t <container-name>(node-fsfe) .` - Build docker container in the current location

`sudo docker image ls` - Check current docker images

`pm2 stop index-ws.js` - Stop current pm2 process to use port for our docker container 

`sudo docker run -d -p <server-port>(3000):<docker-port>(3000) <docker-image-name>(node-fsfe)` - Run docker image on indicated port

<br>

### Orchestration & Load Balancing 

`htop` - Check server current processes and resources usage 

`sudo docker run -d -p <server-port>(3001):<docker-port>(3000) <docker-image-name>(node-fsfe)` - Run docker image on indicated port (second instance in port 3001)

`sudo vi /etc/nginx/nginx.conf` - Open nginx config file

Add these lines inside *http* block:

	upstream nodebackend {
		server localhost:3000;
		server localhost:3001;
	}

`sudo vi /etc/nginx/sites-enabled/<domain-name/app-name>(fsfe)` - Open site nginx configuration

Change this line:

	proxy_pass http://127.0.0.1:3000/;
	
For:

	proxy_pass http://nodebackend;
	
`sudo nginx -t` - Check nginx configuration files

`sudo service nginx restart` - Restart nginx service
	







 
 
 

































 
 















