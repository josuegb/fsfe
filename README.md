
# Frontend masters:  full stack frontend 

## Commands

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



