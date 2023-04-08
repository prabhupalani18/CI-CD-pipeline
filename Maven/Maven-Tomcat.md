# Deploying java app on tomcat server

- For creating and deploying the java app, I have created the pipeline using jenkins server. I have hosted the jenkins server in Ubuntu virtualbox.

### Steps to setup the Ubuntu in virtualbox:

Step 1: Download and install VirtualBox
The first step is to download and install VirtualBox, which is a free and open-source virtualization software that allows you to run multiple operating systems on a single machine. You can download VirtualBox from the official website, and then follow the installation instructions.

Step 2: Download Ubuntu ISO
Next, you need to download the Ubuntu ISO file from the official Ubuntu website. Choose the version you want to install (we recommend the LTS version), and then download the ISO file.

Step 3: Create a new virtual machine
Open VirtualBox and click on the "New" button to create a new virtual machine. Give it a name (e.g. "Ubuntu"), and choose "Linux" as the type and "Ubuntu" as the version. Set the amount of RAM you want to allocate to the virtual machine, and then click "Create".

Step 4: Configure the virtual machine
In the VirtualBox Manager, select the newly created virtual machine and click on "Settings". In the "System" tab, make sure that "Enable EFI (special OSes only)" is unchecked. In the "Storage" tab, click on the "Empty" CD/DVD icon under the "Controller: IDE" section, and then click on the "Choose Virtual Optical Disk File" button to select the Ubuntu ISO file you downloaded in Step 2.

Step 5: Start the virtual machine
Click on the "Start" button to start the virtual machine. The Ubuntu installation process should start automatically. Follow the on-screen instructions to install Ubuntu.

Step 6: Install the VirtualBox Guest Additions
Once Ubuntu is installed, shut down the virtual machine and then start it again. Go to the "Devices" menu and select "Insert Guest Additions CD image". This will mount a virtual CD with the VirtualBox Guest Additions software. Open a terminal and run the following command to install the software:

```sh
sudo sh /media/$USER/VBox_GAs_<version>/VBoxLinuxAdditions.run
```

Replace <version> with the version number of the Guest Additions. Follow the on-screen instructions to complete the installation.
  
### Install Jenkins in Ubuntu:
  
  The version of Jenkins included with the default Ubuntu packages is often behind the latest available version from the project itself. To ensure you have the latest fixes and features, use the project-maintained packages to install Jenkins.

First, add the repository key to your system:
  ```sh
  wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key |sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
  ```
  The ```gpg --dearmor``` command is used to convert the key into a format that apt recognizes.

Next, let’s append the Debian package repository address to the server’s ```sources.list```:
  ```
  sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins.gpg] http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  ```
  The ```[signed-by=/usr/share/keyrings/jenkins.gpg]``` portion of the line ensures that ```apt``` will verify files in the repository using the GPG key that you just downloaded.
  
  After both commands have been entered, run ```apt update``` so that ```apt``` will use the new repository.
  ```sh
  sudo apt update
  ```
  
  Finally, install Jenkins and its dependencies:
  ```sh
  sudo apt install jenkins
  ```
  
  ### Start and Configure Jenkins
  
  now that Jenkins is installed, start it by using ```systemctl```:
  ```sh
  sudo systemctl start jenkins.service
  ```
  Since ```systemctl``` doesn’t display status output, we’ll use the ```status``` command to verify that Jenkins started successfully:
  ```sh
  sudo systemctl status jenkins
  ```
  If everything went well, the beginning of the status output shows that the service is active and configured to start at boot.
  Now that Jenkins is up and running, adjust your firewall rules so that you can reach it from a web browser to complete the initial setup.
  
  By default, Jenkins runs on port ```8080```. Open that port using ```ufw```:
  ```sh
  sudo ufw allow 8080
  ```
  - Note: If the firewall is inactive, the following commands will allow OpenSSH and enable the firewall:
  ```sh
  sudo ufw allow OpenSSH
  sudo ufw enable
  ```
  Check ufw’s status to confirm the new rules:
  ```sh
  sudo ufw status
  ```
  You’ll notice that traffic is allowed to port ```8080``` from anywhere.
  
  With Jenkins installed and a firewall configured, you have completed the installation stage and can continue with configuring Jenkins.
  - Configure Jenkins
  
  To set up your installation, visit Jenkins on its default port, 8080, using your server domain name or IP address: ```http://your_server_ip_or_domain:8080```
  
  You should receive the Unlock Jenkins screen, which displays the location of the initial password;
  In the terminal window, use the ```cat``` command to display the password.
  ```sh
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```
  Copy the 32-character alphanumeric password from the terminal and paste it into the Administrator password field, then click Continue.
  The next screen presents the option of installing suggested plugins or selecting specific plugins - close that window as we can install our required plugins later.
  
  ### Install Tomcat server
  
  For security purposes, Tomcat should run under a separate, unprivileged user. Run the following command to create a user called ```tomcat```:
  ```sh
  sudo useradd -m -d /opt/tomcat -U -s /bin/false tomcat
  ```
  By supplying ```/bin/false``` as the user’s default shell, you ensure that it’s not possible to log in as ```tomcat```.
  
  Download the archive using wget by running the following command:
  ```sh
  wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.7/bin/apache-tomcat-10.1.7.tar.gz
  ```
  The ```wget``` command downloads resources from the Internet.
  
  Then, extract the archive you downloaded by running:
  ```sh
  sudo tar xzvf apache-tomcat-10*tar.gz -C /opt/tomcat --strip-components=1
  ```
  Since you have already created a user, you can now grant tomcat ownership over the extracted installation by running:
  ```sh
  sudo chown -R tomcat:tomcat /opt/tomcat/
  sudo chmod -R u+x /opt/tomcat/bin
  ```
  ### Optional
  Change tomcat server default port address incase that is already in use:
  - Go to ```tomcat>conf``` folder
  - Search "Connector port"
  - Replace "8080" by ```your port number```
  - Restart tomcat server.
  
  Access tomcat application from browser on port 8090  
 - http://<Public_IP>:```your port number```

### Configure Tomcat
  
Now application is accessible on ```your port number``` or default value 8080. but tomcat application doesnt allow to login from browser. changing a default parameter in context.xml does address this issue
   ```sh
   #search for context.xml
   find / -name context.xml
   ```
Above command gives 3 context.xml files. comment (<!-- & -->) `Value ClassName` field on files which are under webapp directory. 
After that restart tomcat services to effect these changes. 
At the time of writing this lecture below 2 files are updated. 
   ```sh 
   /opt/tomcat/webapps/host-manager/META-INF/context.xml
   /opt/tomcat/webapps/manager/META-INF/context.xml
   
   # Restart tomcat services
   tomcatdown  
   tomcatup
   ```
  Update users information in the tomcat-users.xml file
goto tomcat home directory and Add below users to conf/tomcat-users.xml file
```sh
	<role rolename="manager-gui"/>
	<role rolename="manager-script"/>
	<role rolename="manager-jmx"/>
	<role rolename="manager-status"/>
	<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
	<user username="deployer" password="deployer" roles="manager-script"/>
	<user username="tomcat" password="s3cret" roles="manager-gui"/>
   ```
Restart serivce and try to login to tomcat application from the browser. This time it should be Successful
