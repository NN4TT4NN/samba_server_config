<div style="display: flex; align-items: center; margin-bottom: 3%">
        <img src="assets/images/windows_logo.svg" width="5%" />
        <img src="assets/images/seta.svg" width="3%" />
        <img src="assets/images/linux_logo.svg" width="5%" />
</div>

[![Typing SVG](https://readme-typing-svg.herokuapp.com?font=Fira+Code&color=12F700&pause=1000&random=false&width=800&lines=Sharing+files+with+Windows+through+Linux+Samba+Server.)](https://git.io/typing-svg)

Today we will learn how to create a shared server on Linux so that Windows users can access it directly.

I decided to create this repository to show how we can configure shared remote access for customers who use local physical servers or cloud structures.

**NOTE:** _you're free to create your linux machine whathever way you like or feel confortable with, in this particular case, i'm creating this one at Azure Cloud_

Without further ado... let's get to the point.

# Sudo update everything and install samba

Assuming that you already have your linux machine, let's get those packages and dependencies updated:

````
sudo apt update && sudo apt upgrade
````

Now let's install the Samba tools to begin setting up access.

````
sudo apt install -y samba
````
I personally like to have these two tools installed whem it comes to working with networks: 

- [Wireless Tools](https://hewlettpackard.github.io/wireless-tools/Tools.html) 
- [Net Tools](https://www.kali.org/tools/net-tools/).

So, i'm installing it using the following command:

````
sudo apt install -y net-tools wireless-tools
````

You will also need the `systemctl` command, and to set it up, you can follow this tutorial here to get it up and running:

- [Setting up Debian on WSL2 with Systemd](https://avivarma1.medium.com/setting-up-debian-on-wsl2-with-systemd-fb4831dd7b82) 

**NOTE:** _if you're using a Azure Cloud Virtual Machine just like me you probably don't need to do anything about systemctl_

<br/>
<br/>
<br/>

# Creating users, groups, and limiting access

In our scenario, we will create a folder where people can share their files and information.

To do this, let's create a folder in the directory "**/var/shared-folder-name**".

**Note:** _You can give the folder any name you prefer, but in this "**educational**" context, we will create it with the name "**contabilidade**"._

````
mkdir /var/contabilidade
````

````
ls /var
````

Now let's create the owner group of "**contabilidade**" folder.

````
addgroup contabilidade
````

And after that, create the user that will belong to that group.

````
adduser eliz --ingroup contabilidade
````

You can check the "**contabilidade**" folder's permission running the "**ls -l /var/**"

As you can see, the folder belongs to root.

The first "**root**" refers to the folder's owner.

The second one refers to the group.

To change this run the following command

````
chgrp contabilidade /var/contabilidade
````

Once you changed the group permisions, it's time to change the folder permissions.

````
chmod 770 /var/contabilidade/
````

<br/>
<br/>
<br/>

# Creating samba user access 

This is an important step, because if you overstep or miss it, there's no way it can work.

So, for the user be able to access tha Linux server into the Windows machine, we need to create a user inside the samba server.

As we previously created the user **eliz**, now we need to give her a password to authenticate into the "**contabilidade**".

````
smb passwd -a eliz
````

<br/>
<br/>
<br/>


# Structuring SMB Connection and Protocols

You can learn more about these tools on [samba.org](https://www.samba.org/).

After installing the packages, we need to access the Samba configuration file (**smb.conf**).

For security reasons, I like to make a local backup of the configuration file.

````
cd  /etc/samba
````

````
cp smb.conf smb.conf.bkp
````

You can check using the "**cat**" command.

````
cat smb.conf.bkp
````
As you can see, this file has a lot of comments which can clutter it up.

So let's copy only the content that interests us into **smb.conf**:

`````
egrep -v "^#|^$" smb.conf.bkp > smb.conf
`````

Now, verify if the file is correct and the information is there:

````
cat smb.conf.bkp
````

Once you have the clean file, let's open nano to edit its content:

````
nano /etc/samba/smb.conf
````

For this specific case i did erase all the content above global section.

And then added the "**contabilidade**" configuration.

`````
[contabilidade]
        path = /var/contabilidade
        valid users = @contabilidade
        write list = @contabilidade
        browseable = yes
        writable = yes
`````

- **path = /var/contabilidade** - is the shared folder's path
- **valid users = @contabilidade** - refers to the allowed users of this folder
- **write list = @contabilidade** - means that only this group can write into the contabilidade folder
- **browseable = yes** - it makes sure that you can browse the files into your Windows machine

**NOTE:** _you can check out the scripts folder in this repository, there's a configuration file **smb.conf**, you can take a look at the configuration and it's explaning in the bottom of that file._


<br/>
<br/>
<br/>

# Authenticating 

Now, everything you need to do is copy and paste the Linux Machine ip address into the Windows execution command.

`````
\\172.21.176.218\contabilidade
`````

You'll be asked for Samba's user crendentials that you have created previously.

**NOTE:** _remember to configure the firewall for allowing tcp and udp traffic at port 445, that is the default port for smb protocol communication between Windows machines._

<hr/>
<br/>

[![Typing SVG](https://readme-typing-svg.herokuapp.com?font=Fira+Code&color=12F700&pause=1000&random=false&width=1000&lines=Thank+you+for+reading+this+...+i+hope+it+can+help+you+guys.;Happy+Hacking!)](https://git.io/typing-svg)
