# Setting up Development Env
## installation of Vagrant, Virtual box and Ruby
![](dev_env.png)
### vagrant commands
- ` vagrant up` to launch the vm
- `vagrant destroy` to delete everything 
- `vagrant reload` to reload any new instruction in our `Vagrantfile`
- `vagrant halt` it poses the vm
- `vagrant status`

#### More commands can be found by typing `vagrant` on gitbash terminal and you'll see below outcome
```
Common commands:
     box             manages boxes: installation, removal, etc.
     cloud           manages everything related to Vagrant Cloud
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     hostsupdater
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     upload          upload to machine via communicator
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version
     winrm           executes commands on a machine via WinRM
     winrm-config    outputs WinRM configuration to connect to the machine

For help on any individual command run `vagrant COMMAND -h`
```
- Let's `ssh` into our vm and launch nginx web-server
- use `apt-get` package manager in Linux - for mac `homebrew` or only `brew`
- `apt-get` is used to install and uninstall any packages needed
- To use the command in `admin` mode we can use `sudo` before the command
- `sudo apt-get upgrade -y`
- `sudo apt-get update -y`
- `ping www.bbc.co.uk`
- `sudo apt-get install name_of_the_package` 
- To work in an `admin mode` at all times (not recommonded) `sudo -su` and you'll land in admin mode
- We will install nginx in our guest machine/VM/ubuntu 16.04
- launch the default nginx page in host machine's browser
- To come out of your VM `exit`
- install nginx `sudo apt-get install nginx -y`
- checking status of nginx ` systemctl status nginx` 
- or restrat ` systemctl restart nginx` or just to start ` systemctl start nginx` 



- step 1: Install the plugin `vagrant plugin install vagrant-hostsupdater`
- step 2: vagrant destroy
- step 3: add this line of code into Vagrantfile `config.hostsupdater.aliases = ["development.local"]`
- step 4: vagrant up
- step 5: vagrant ssh
- step 6: repeat all the update and upgrade commands followed by installing nginx and checking of nginx
- `ningx status active` should load nginx in the browswer with `192.168.10.100` as well as `http://development.local/`

**Reload/Destroy vagrant**
- 1) `vagrant reload` (if this does not work, you will have to `vagrant destroy` and then do `vagrant up` again)
- 2) `vagrant ssh` Opens VM
- 3) `systemctl status nginx` 
- 4) Go to browser and write: http://192.168.10.100
- ---------------------------------------------------- 
- Lets automate the tast that we did manually earlier today
- Create a file called `provision.sh`. Add the below code to the new file: 

- `#!/bin/bash` (necessary for all bash scripts)
- `sudo apt-get update -y`
- `sude apt-get upgrade -y`
- `sudo apt-get install nginx -y`
- `systemctl status nginx`

- To run provision.sh we need to give file permission and make this file exceutable
- To change permission we use `chmod` with required permission then file name
- `sudo chmod +x provision.sh`
-To run `sudo ./provision.sh`
- ---------------------------------------------------- 
### Reverse proxy setup
Head over to /etc/nginx/sites-available and put this code in the default file:
```
server {
  listen 80;

  server_name _;

  location / {
    proxy_pass http://192.168.10.100:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
```
- Test the file with `sudo nginx -t`
- Restart and check to make sure it is running a fresh instance with `sudo systemctl restart nginx` and `sudo systemctl status nginx`
- Head over back to your app folder and run `npm start`
