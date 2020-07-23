# Azure - Linux virtual machine

## Purpose

At the end of this module, you will:
* Understand the options that are available for virtual machines in Azure
* Create a Linux virtual machine using the Azure portal
* Connect to a running Linux virtual machine using SSH
* Install software and change the network configuration using the Azure portal

## Creating a new Linux virtual machine

We can create Linux VMs with the Azure portal, Azure CLI, or Azure PowerShell. The easiest approach is the portal because it walks you through the required information and provides hints and helpful messages during the creation of the VM.

### ![Building a Linux VM][activity] 2.20.1 Generating a SSH Key

Secure Shell (SSH) is an encrypted connection protocol that allows secure sign-ins over unsecured connections. SSH allows you to connect to a terminal shell from a remote location using a network connection.

There are two approaches we can use to authenticate an SSH connection: username and password, or an SSH key pair.

With an SSH key pair, you can sign in to Linux-based Azure virtual machines without a password. This is a more secure approach if you only plan to sign in to the VM from a few computers. If you need to be able to access the Linux VM from a variety of locations, a username and password combination might be a better approach. There are two parts to an SSH key pair: a public key and a private key.

The public key is placed on your Linux VM or any other service that you wish to use with public-key cryptography. This can be shared with anyone.

The private key is what you present to verify your identity to your Linux VM when you make an SSH connection. Consider this confidential information and protect this like you would a password or any other private data.

You can use the same single public-private key pair to access multiple Azure VMs and services.

TL;DR;

To create a public-private key pair we use ssh-keygen available in tools like GitBash or Azures own Command in the Cloud called Cloud Shell.

1. Sign into the Azure portal using the same account.

![Cloud Shell](../images/cloud-shell-bash.png)

2. In the menu at the top of the page, select the >_ icon to open Cloud Shell.

![SSH](../images/ssh-gen.png)

3. Make sure the CloudShell says Bash in the upper left. If it says PowerShell, use the drop-down to select Bash and select Confirm to change to the Bash shell.

4. Type this command to create the ssh keys (bogth the Private and the Public):

```
ssh-keygen -t rsa -b 2048
```

5. You will be prompted to enter a file in which to save the key pair. Just press Enter to save in the default location, listed in brackets.

6. You will be asked to enter a passphrase. You can type a passphrase for your SSH key or press Enter to continue without a passphrase. It's easy for training not to use a passphrase.

7. The ssh-keygen command generates public and private keys with the default name of id_rsa in the ~/.ssh directory. Use the path to the public and private keys to display its contents with cat by typing **cat ~/.ssh/id_rsa.pub** and also **cat ~/.ssh/id_rsa** 

>There's two files!!! You need to save both out to text files!!!

8. Copy the output of these commands and save it somewhere, such as a text file to use later in this exercise.

9. This is your private key and you will need it when configuring your administrator account and also to log in to your VM.

10. Do NOT close the Cloud Shell, please leave it running and open a new Browser Tab.

### ![Building a Windows VM][activity] 2.20.2 Building a Linux VM

1. In a new Browser Tab viewing the Azure portal, select Create a resource.

1. In the search box, enter Ubuntu Server.

1. Select Ubuntu Server 18.04 LTS Canonical from the list.

1. Click the Create button to start configuring the VM.

#### 2.20.3 Configure the VM settings

Same as Windows the VM creation experience in the portal is presented in a "wizard" format to walk you through all the configuration areas for the VM.

![Configure the VM](../images/configure-lvm-1.png)

1. Select the Subscription that should be billed for VM hours.

1. For Resource group, create a new "VMLinuxRG" resource group.

1. In the Instance Details section, enter a name for your VM, such as NCGNLIDA0001 (for Linux Internal Desktop Application).

>It's best practice to standardise your resource names so you can easily identify their purpose. Linux VM names are a bit limited - they must be between 1 and 64 characters, cannot contain non-ASCII or special characters, and must be unique in the current resource group.

1. Select Australia Southeast region.

1. Set Availability options as "No Infrastructure redundancy required". This option is used to ensure the VM is highly available by grouping multiple VMs together to deal with planned or unplanned maintenance events or outages.

1. Ensure the image is set to "Ubuntu Server 18.04 LTS Canonical". You can open the drop-down list to see all the options available.

1. The Size field is not directly editable and has a D2sv3 default size. Click the Change size link to explore other VM sizes.

1. In the Administrator Account section, select the SSH public key option.

1. Paste the SSH key from your public key file you created in the previous unit into the SSH public key field. It should look similar to the example shown above with no additional whitespace or line-feed characters.

1. In the Inbound Port Rules section, open the list and choose Allow selected ports. Since this is a Linux VM, we want to be able to access the desktop using SSH. Scroll the list if necessary until you find SSH (22) and select it. As the note in the UI indicates, unlike VNETs, we can adjust the network ports after we create the VM.

#### 2.20.4 Configure Disks for the VM

![Configure the VM](../images/configure-vm-3.jpg)

1. Click Next to move to the Disks tab. Recall with your everyday computer you get an operating system which is typically installed on a disk (C:) and Temporary disk (D:). Let's add an extra data disk as well.

1. Expand Advanced and select managed disks so we don't have to work with storage accounts. You can flip the switch in the GUI to see the difference in information Azure needs if you like.

1. Click the Create and attach a new disk link in the DATA DISKS section.

![Configure the DataDisk](../images/configure-vm-4.jpg)

4. You can accept all the defaults: Premium SSD, 1023 GB, and None (empty disk); 

> Notice here is where we could use a snapshot, or Storage Blob to create a VHD.

5. Click OK to create the disk and go back to the DATA DISKS section.

6. There should now be a new disk in the first row.

![Configure the DataDisk](../images/configure-vm-5.jpg)

#### 2.20.5 Configure the Network

1. Click Next to move to the Networking tab.

1. Create a new VNet or use the one we created for the Windows machine under Virtual Network.

> If you can't see the Virtual Network you created for the Windows machine its because you've specified a different region.

3. Click OK.

#### 2.20.6 Configure the Management

1. Turn off Boot diagnostics, leaving this enabled will create a boot log file in a Storage account that is not needed for this exercise.

#### 2.20.7 Finish configuring the VM and create the image

1. The rest of the options have reasonable defaults and there's no need to change any of them. You can explore the other tabs if you like. The individual options have an (i) icon next to them that will show a help bubble to explain the option. This is a great way to learn about the various options you can use to configure the VM.

1. Click the Review + create button at the bottom of the panel.

1. The system will validate and give you details about the VM being created.

1. Click Create to deploy the VM. The Azure dashboard will show the VM that's being deployed. This may take several minutes depending on the size of the VM.

1. Once it's completed, browse to the Resource and from the Overview copy the IP Address of the VM you just created and save the IP Address to notepad.

#### 2.20.8 Use PuttyGEN to convert the Private Key to a PPK file

This step is important in order to continue.

1. Download and install the Putty tools from https://www.putty.org/ or direct https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

1. You should have the Private key saved from the Cloud Shell session or a GitBash command line to a text file, eg:

```
-----BEGIN DSA PRIVATE KEY-----
MIIBugIBAAKBgQDTpIlC4HDT8+R7nALILZfh9zA93J9r9CYwrvNFIUa+43toyABg
vY2P2gwsC1xPraujPYb2+AVZPmyrbtjmnw+4zIXufcz6mw4nlYJfc5Rd0Ms00uWB
uMmQlqOYMG6HJHjyVmMd631rGu9lQeSTwzDq6H9BZl9keuxmgVB4nF651QIVAOdB
c8AtqassvzluUF3a1Y9L+zt8Bf7Xo7vU0wvpEEnjKG4Lcl2SeO8Eoqb1wZcDbDJC
wqv0g+PG8dI4uqboFPqAIr4/s/U+/REW81PHx7wjfhaBe2GXoBAbrQIUXZzuw+aG
ERrD9L4URL9HP2GbWno=
-----END DSA PRIVATE KEY-----
```

3. Open the PuTTYGen application.

4. Click Load button and open the text file with the private key (TIP: in the Open Dialog, change drop downlist from "PuTTY Private Key Files" to "All Files" so you can see and select the file). 

![PuTTY Gen](../images/PuTTYGEN.png)

5. Lastly click the Save Private key button under the Load button and save the file out with a PPK file extension. 

Please don't skip this step!!


#### 2.20.9 Login to the VM - via Putty

1. Open PuTTY (not PuttyGen).

1. Paste in the IP addrress you copied to notepad into the Hst Name (or IP Address) field.

![PuTTY session](../images/PuttyLogin.png)

3. Specify the ppk file you converted with PuTTYGen (Connection > SSH > Auth > Browse).

![PuTTY session](../images/PuttyLogin2.png)

4. Click Open.

5. Login with the username you specified.

![PuTTY session](../images/PuttyLogin3.png)

#### 2.20.10 Alternatively Login to the VM - via CloudShell

> We are using PuTTY for this exercise, this is optional, please skip to 2.20.11 if you're ok with PuTTY.


1. Select the Linxu VM.

1. Click the Connect > SSH at the top of the pane.

1. Under step 4, click the Copy button to copy the command to the clipboard and run it in CloudShell.

![Configure the Network](../images/ssh-connect.png)

#### 2.20.11 Install NGINX

1. In the Linux VMs PuTTY SSH session run these commands to install NGINX

```
sudo apt-get update
sudo apt-get install -y nginx
```

1. Once that's installed copy the Public IP Address of the VM, open Chrome or FF and Browse to http://&lt;Public IP Address&gt; and you won't see NGINX home page yet.

1. In Azure Portal check the overview of the VM and see if it is running using the 4 Charts. 

1. We need to add a rule for Port 80 because we are exposing internet traffic over HTTP. Click Networking 

1. Click Add inbound port rule.  

![Configure the Network](../images/AddPort80.png)

6. Specify Source Any, Source port range * and Desination Any and Desination Port 80 as illustrated:

![Configure the Network](../images/AddPort80-1.png)

7. Go to the Overview sub menu, copy the public IP address into the browser to see the NGINX home page - give it a minute or so for the security groups to take affect.

That completes this module setting up a Linux VM that we will use in a further chapter. 


[activity]: ../icons/activity.png "Workshop Activity!"
[discussion]: ../icons/discussion.png "Team Discussion!"
[reading]: ../icons/reading.png "Further Reading!"
