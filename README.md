# Automated Server
The goal of this guide is to provide a basic list of steps for creating a fresh cloud server setup. The setup is geared for the purpose of automatic deployment for APIs and Webapps using Github Actions. I am not a profesional at server configuration and my guide could potentially lead to sercurity risks that I'm unaware of. However, this infomation is mostly gathered from various other guides which I assume was written by server configuration experts.

Unless noted otherwise, anything inclosed in angle brackets "<" and ">" eg. `<example>` should be assumed to be a placeholder, and the actual input is up to the person following this guide or their specific use case.

### Linode specific manual steps [Jun 2022]
1. Login to https://login.linode.com/login
2. On linodes page (accessed by left menu at top or https://cloud.linode.com/linodes) click “Create Linode” button located in the top right
3. For “Choose a Distribution” select latest version of Ubuntu LTS
4. For “Region” select Dallas, TX (or what makes the most sense)
5. For “Linode Plan” choose “Shared CPU” tab and select “Linode 2 GB” option (or what you need accordingly)
6. Give a Label for your instance under “Linode Label”
7. (optional) create or select a tag(s) under “Add Tags”
8. Create a secure root password under “Root Password”  
   (I recommend using a password generator site like https://passwordsgenerator.net/ with a length of 16 characters)
9. Click Create Linode
10. Wait for “Running” indicator at top left of https://cloud.linode.com/linodes/<linode-id>
### Setting up server with SSH [Jun 2022]:
1. Locate your server IP address (listed on https://cloud.linode.com/linodes/<linode-id> for linode)
2. Open a terminal on your machine (Windows 10 and 11 have this built in by default now I think, if not other options are available)
3. In your terminal `ssh root@<ip address>` hit enter  
  It might ask you if you want to continue since it’s your first time connecting to this machine, select yes
4. Update the system `apt update && apt upgrade` for Ubuntu and Debian  
If it asks if you want to continue, select yes  
If you get a message about upgrading kernel, reboot after finishing  
If asked what services should be restarted keep the default options (or selected more if you know what you're doing)  
If logged out of ssh log back in (refer to step 3)
5. Logout of ssh `exit`
6. Reboot (for linode go to https://cloud.linode.com/linodes/<linode-id> and click reboot in top right)
7. Login (step 3) and repeat steps 4 - 6 (probably not necessary) if step 4 renders no results (‘0 upgraded, 0 installed, 0 to remove, etc.’) no need to repeat step 5 and 6
8. Create a limited user `adduser limited`  
  (“limited” can be any username you want but for compatibility with scripting later set it as “limited”, might have a dynamic option later)  
  Give it a secure password  
  You can use the default setup options here  
  then add that user to sudo group `adduser limited sudo`
9. Switch to limited user `su - limited` and create a ssh directory `mkdir -p ~/.ssh && sudo chmod -R 700 ~/.ssh/`
10. Logout (step 5)
11. Verify that you don’t have ssh keys generated yet `ls ~/.ssh/id_rsa*`,  
  if you do then don’t follow the next instruction,  
  generate a key pair `ssh-keygen -b 4096`,  
  for simplicity use the default “id_rsa” and “id_rsa.pub”, add a passphrase if you’d like
12. Add your .pub key (MacOS specific) to the server `scp ~/.ssh/id_rsa.pub limited@<ipv4 address>:~/.ssh/authorized_keys`
13. Login to the limited user `ssh limited@<ip address>`
14. Type `sudo chmod -R 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`
15. Logout (step 5)
### Finish Configuration with Scripts
You should now be ready to configure the rest of the server through scripting. If you would prefer to continue with manual configuration read section:  
   "(Continued) Setting up server with SSH [Jun 2022]:"

### (Continued) Setting up server with SSH [Jun 2022]:
1. Login to limited user `ssh limited@<ipv4 address>`  
   you should no longer need to type a password when logging in if everything was done correctly
2. Set hostname by typing `sudo hostnamectl set-hostname <example-hostname>` (could be whatever you want but should be memorable or descriptive)
3. Logout `exit`
4. Log back in (step 1) to verify hostname change at top of terminal window, and/or type `hostname` after logging in
7. Edit your "hosts" file to configure domain name  
   Type `sudo vim /etc/hosts`  
   at the bottom add 3 lines:
   ```
   127.0.0.1 localhost.<your domain name> localhost
   <ipv4 address> <your hostname>.<your domain name> <your hostname>
   <ipv6 address> <your hostname>.<your domain name> <your hostname>
   ```
   then save the file
8. (optional) Change timezone  
  `timedatectl list-timezones` to list timezones,  
  then `timedatectl set-timezone '<timezone you want from list>'`
9. Type `date` to verify your timezone is set correctly
10. Now disallow login into root user and disallow logging in with password by editing `sudo vim /etc/ssh/sshd_config`,  
    edit line `PermitRootLogin yes` to `PermitRootLogin no`,  
    edit line `PasswordAuthentication yes` to `PasswordAuthentication no`  
    edit `#AddressFamily any` to `AddressFamily inet`  
    Save sshd_config and then type sudo systemctl restart sshd
11. Logout (step 3) and then to verify the last steps try logging into root with `ssh root@<ipv4 address>`,  
   after confirming you can't login to root, log back in with limited user (step 21)
12. To setup a firewall run `sudo apt-get install ufw` 
      To allow all outgoing run `sudo ufw default allow outgoing`  
      To deny all incoming run `sudo ufw default deny incoming`  
      To allow ssh run `sudo ufw allow ssh`
      To allow http run `sudo ufw allow http/tcp` and https `sudo ufw allow https/tcp`
      Enable ufw with `sudo ufw enable`
      Check rules with `sudo ufw status`
13. (optional) Logout (step 3) on compatible OSs edit “hosts” file to include the hostname created in step 2,  
      type `sudo vim /etc/hosts` and add the line `<ipv4 server address> <hostname>` at the bottom,  
      this will allow you to login with `ssh <username>@<hostname>` which is easier than finding/remembering the ipv4 address like in step 1
Done!

