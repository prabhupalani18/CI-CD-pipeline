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
