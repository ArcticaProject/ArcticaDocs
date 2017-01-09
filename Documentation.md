## Arctica Greeter - Installation Guide for Ubuntu <xml> 16.04 LTS (Latest version)<xml/> ##

#<span style="color:darkblue; font-family: Cambria;"> Introduction <span/>#

<span style="color: black; font-family: Arial; font-size: 14;">
The following document, documents how to install Ubuntu with Arctica Greeter. Make sure when typing the commands for installation, since Ubuntu differ between uppercase and lowercase letters because everything is case-sensitive. Everything that is highlighted with a black background is what you would type in the Terminal.
</span>

##<span style="color:#0080ff; font-family: Cambria;"> Stage one <span/>##
<span style="color: black; font-family: Arial; font-size: 14;"> 
**Install Ubuntu:**
When installing Ubuntu make sure to check both boxes, **Download Updates While Installing Ubuntu** and **Installation of 3rd party applications**, when they both are ticked then press continue. </span>
![1](https://github.com/Korn20000/Testing/blob/master/img/1.png?raw=true)

<span style="color: black; font-family: Arial; font-size: 14;">
Next step choose **Erase Disk** and press Install Now.
If it asks you **“Write the changes to disk?”**, ignore it and press continue.
</span>

![2](https://github.com/Korn20000/Testing/blob/master/img/2.png?raw=true)

<span style="color: black; font-family: Arial; font-size: 14;"> 
Next choose location where you live, after the location has been chosen you will get the opportunity to choose your keyboard layout so it can fit to your language.</span>

<span style="color: black; font-family: Arial; font-size: 14;">
At the end it will ask you to fill in a form with name, computer name, username and password. Make sure that you do not login automatically since it will give complications with login on Arctica Greeter.</span>

![3](https://github.com/Korn20000/Testing/blob/master/img/3.png?raw=true)

<span style="color: black; font-family: Arial; font-size: 14;">Restart when the installation is done.</span>

##<span style="color:#0080ff; font-family: Cambria;"> Stage two <span/>##

<span style="color: black; font-family: Arial; font-size: 14;">
After Ubuntu is successfully installed open a terminal either by pressing **CTRL+ALT+T** or navigate to **“Search your computer”** and search for terminal.</span>
![4](https://github.com/Korn20000/Testing/blob/master/img/4.png?raw=true)

<span style="color: black; font-family: Arial; font-size: 14;">
When the **“Terminal”** appears you will see a window similar to the picture below.</span>
![5](https://github.com/Korn20000/Testing/blob/master/img/5.png?raw=true)

Now that the **“Terminal”** is open run the following commands.

- <span style="color:#f0e9e9; background-color:#5a4b4b;">**sudo -i**</span> 
<span style="color: black; font-family: Arial; font-size: 14;">(This will give you access as an Admin/Superuser/Root.)
	- **NOTE:** After the command “**sudo -i**” it will ask you for a password, the password is the same you are using for your login. Also **NOTE** that it will not show your password when typed, it will instead keep a blank field while typing, so type it carefully and press enter.</span><br></br>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get update**</span> <span style="color: black; font-family: Arial; font-size: 14;">(This will give you the latest available package version in the repositiory.)
	-	<span style="color: black; font-family: Arial; font-size: 14;">**NOTE:** it will ask you at the end if you want to continue, for this type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**y**</span>” and press enter.</span>

<span style="color: black; font-family: Arial; font-size: 14;">When it is done with getting the latest package version, you will need to download and install them.</span>

- <span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get upgrade**</span> <span style="color: black; font-family: Arial; font-size: 14;">(This will download and Install the updates, this might take some time to complete.)</span>

	- <span style="color: black; font-family: Arial; font-size: 14;">**NOTE:** it will ask you at the end if you want to continue, for this type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**y**</span>” and press enter.</span>

<span style="color: black; font-family: Arial; font-size: 14;">**NOTE: you can distinguish between Admin and User in the Terminal. A user has a $-sign when you are logged in a user, and #-sign if you are**</span>

##<span style="color:#0080ff; font-family: Cambria;"> Stage three <span/>##

<span style="color: black; font-family: Arial; font-size: 14;">While you are still logged in as Admin/Superuser/ROOT you will need to add the repository.</span>

<span style="color: black; font-family: Arial; font-size: 14;">Type following:</span>

- <span style="color:#f0e9e9; background-color:#5a4b4b;">**nano /etc/apt/sources.list** </span>

<span style="color: black; font-family: Arial; font-size: 14;">Head to the bottom and press enter to insert new line and type following:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**deb http://packages.arctica-project.org/ubuntu-nightly xenial main**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now write out the repository by pressing “**CTRL+O**”, it will ask you to give the “**File Name to Write**” press enter to that.

<p>After you have saved it, press “**CTRL+X**” to exit the repository.

<span style="color: black; font-family: Arial; font-size: 14;">You should now be back in the Terminal, type the following command to fetch the key from a server:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A8680F5598DE3101**</span>

<span style="color: black; font-family: Arial; font-size: 14;">It will tell you that a “**RSA: 1 is imported**” if it succeeded to import the key.</span>

<span style="color: black; font-family: Arial; font-size: 14;">Next you need to install git, type following command, it will ask you if you want to continue type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**y**</span>” and press enter:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install git**</span>

<span style="color: black; font-family: Arial; font-size: 14;">When the installation of Git is done, you will need to create a folder in order to clone git so they are collected in one folder. Therefore you need to type following command so you can create a folder:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**mkdir arctica-greeter**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now open the folder that you just created by using following command:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd arctica-greeter**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now you are able to clone the 4 repositories from ArcticaProject, so type following lines one by one:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**git clone https://github.com/ArcticaProject/arctica-greeter/**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**git clone https://github.com/ArcticaProject/libpam-x2go/**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**git clone https://github.com/ArcticaProject/lightdm-remote-session-x2go/**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**git clone https://github.com/ArcticaProject/remote-logon-service/**</span>

<span style="color: black; font-family: Arial; font-size: 14;">If done correctly then the Terminal will tell you, “**Checking connectivity… done**”.</span>


##<span style="color:#0080ff; font-family: Cambria;"> Stage Four <span/>##

<span style="color: black; font-family: Arial; font-size: 14;">Install the dependencies for the four packages which you acquired from Git. For this you will need to run the command “apt-get install” on the following packages. When installing it will sometimes ask you if you want to continue type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**y**</span>” and press enter.</span>

**Example:**

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install debhelper**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install dh-autoreconf**</span>
-	**etc. etc. etc.** 

<span style="color: black; font-family: Arial; font-size: 14;">The following packages that need to be installed:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**debhelper**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**dh-autoreconf**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**fonts-droid-fallback**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**gnome-common**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**gnome-settings-daemon-dev**</span>

<span style="color: black; font-family: Arial; font-size: 14;">In order to install libayatana you will need to update the packages type following:</span>

- <span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get update**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now carry on with “<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install**</span>” followed by the command.</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libayatana-ido3-0.4-dev**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libayatana-indicator3-dev** </span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libcanberra-dev**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libgnome-desktop-3-dev** </span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libgtk-3-dev** </span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**liblightdm-gobject-1-dev**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**valac**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**xvfb**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**fonts-cantarell**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**fonts-cabin**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**gnome-settings-daemon**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Install libpam-x2go dependencies, use the same command “<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install**</span>” followed by the dependencies:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libgtest-dev**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libpam0g-dev**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libssh-dev**</span> 
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cdbs**</span> 
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**socat**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**pyhoca-cli**</span>

 
<span style="color: black; font-family: Arial; font-size: 14;">Install the lightdm-remote-session-x2go dependencies, use the same command “<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install**</span>” followed by the dependencies:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**dh-apparmor**</span>
 
<span style="color: black; font-family: Arial; font-size: 14;">install remote-logon-service dependencies, use the same command “<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install**</span>” followed by the dependencies:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**dbus-test-runner**</span>  
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libdbustest1-dev**</span> 
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libgcrypt11-dev**</span> 
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libjson-glib-dev**</span> 
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libnm-glib-dev** </span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**libsoup2.4-dev**</span>

##<span style="color:#0080ff; font-family: Cambria;"> Stage five <span/>##

<span style="color: black; font-family: Arial; font-size: 14;">Install the devscripts, using same command “<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install**</span>” followed by the dependence:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install devscripts**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now navigate to each repository folder that was created, in order to open the folders you will need to type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd**</span>” in front of each folder, though to go back one folder you will need to type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd ..**</span>”</span>

<span style="color: black; font-family: Arial; font-size: 14;">Type following:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd arctica-greeter**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now you are in the folder here you will need to run the command:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**debuild –uc –us**</span>

<span style="color: black; font-family: Arial; font-size: 14;">exit the folder by typing “<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd ..**</span>” and navigate to the other folders and do the same</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd libpam-x2go**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd lightdm-remote-session-x2go**</span>
-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd remote-logon-service**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Go back to the folder where the 4 repositories were created using the command “<span style="color:#f0e9e9; background-color:#5a4b4b;">**cd ..**</span>” and install the created packages:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**sudo dpkg -i** ***.deb**</span>

<span style="color: black; font-family: Arial; font-size: 14;">In same folder install thin-client-config-agent:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**apt-get install thin-client-config-agent**</span>

##<span style="color:#0080ff; font-family: Cambria;"> Stage six <span/>##

<span style="color: black; font-family: Arial; font-size: 14;">Open the following file:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**nano /etc/lightdm/lightdm.conf.d/90-arctica-greeter.conf**</span>
	
<span style="color: black; font-family: Arial; font-size: 14;">Remove the “<span style="color:#f0e9e9; background-color:#5a4b4b;">**#**</span>” on the following lines:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**#greeter session=arctica-greeter**</span>
- <span style="color:#f0e9e9; background-color:#5a4b4b;">**#greeter-show-manual-login=false**</span>

<span style="color: black; font-family: Arial; font-size: 14;">So the file will look similar to the picture below.</span>
![6](https://github.com/Korn20000/Testing/blob/master/img/6.png?raw=trueraw=true)
<span style="color: black; font-family: Arial; font-size: 14;">

Now press “**CTRL+O**”  and press enter to save the file, afterwards exit it by pressing “**CTRL+X**”.</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now test the Connection to the server and type following:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**pyhoca-cli --new  --server 92.246.17.152  --port 58005 --user testuser --pass testuser  --command UNITY**</span>

<span style="color: black; font-family: Arial; font-size: 14;">If you connect then an x2go session will start and it will show you a new desktop. Exit the desktop by logging out and the press “**CTRL+C**".</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now edit the environment file, open the file by typing:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**nano /etc/environment**</span>

<span style="color: black; font-family: Arial; font-size: 14;">In the environment press down arrow in order to go to a new line and insert following line:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**SERVER_ROOT=http://92.246.17.152:8080/uccs/inifile/**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now press “**CTRL+O**” and press enter to save the file, afterwards exit it by pressing “**CTRL+X**”.</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now run following commands, though NOTE: username and password are both testuser, also when typing password and username do not include **<>**.</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**export SERVER_ROOT**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Type exit and press enter this will put you back to the very beginning, log back in as an Admin/Superuser/ROOT by typing following:</span>


- <span style="color:#f0e9e9; background-color:#5a4b4b;">**sudo su**</span>
	- **type your password as you did in the beginning**

<span style="color: black; font-family: Arial; font-size: 14;">Now type following to get information about remote desktop servers:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**echo <password> | thin-client-config-agent <username>**</span>

<span style="color: black; font-family: Arial; font-size: 14;">In your case it could look like:</span>

- <span style="color:#f0e9e9; background-color:#5a4b4b;">**echo testuser | thin-client-config-agent testuser**</span>

<span style="color: black; font-family: Arial; font-size: 14;">In order to connect to a remote server you have to tell it which IP to ping, therefore open the remote-logon-service file by typing following:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**nano /etc/remote-logon-service.conf**</span>

<span style="color: black; font-family: Arial; font-size: 14;">Change the line “**URI=http://localhost:8080/uccs/inifile/**” to:</span>

-	<span style="color:#f0e9e9; background-color:#5a4b4b;">**URI=http://92.246.17.152:8080/uccs/inifile**/</span>

<span style="color: black; font-family: Arial; font-size: 14;">Now press “**CTRL+O**”  and press enter to save the file, afterwards exit it by pressing “**CTRL+X**”.</span>

<span style="color: black; font-family: Arial; font-size: 14;">Type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**exit**</span>” in order to logout as Admin, type “<span style="color:#f0e9e9; background-color:#5a4b4b;">**exit**</span>” again so it will close the Terminal. Restart the system and you will see Arctica Greeter as your login screen.</span>

##<span style="color:#0080ff; font-family: Cambria;"> Additional notes, how to change resolution <span/>##

http://code.x2go.org/doc/pyhoca-cli/man/man1/pyhoca-cli.1.html jan@fleten.net @ 12:38

look at -g --geometry 12:38

-g, --geometry {<WIDTH>x<HEIGHT>|fullscreen|maximize} 12:38

this is pyhoca-cli commands


