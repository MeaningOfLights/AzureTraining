#  Azure - Serial Console

## Purpose

At the end of this module, you will:
* Learn how to have direct serial-based access to Linux or Windows machines.

## Serial Console access

Managing and running virtual machines can be hard. Azure offer extensive tools to help you manage and secure your VMs, including patching management, configuration management, agent-based scripting, automation, SSH/RDP connectivity, and support for DevOps tooling like Ansible, Chef, and Puppet. However, we have learned from many of you that sometimes this isn’t enough to diagnose and fix issues. Maybe a change you made resulted in an fstab error on Linux and you cannot connect to fix it. Maybe a bcdedit change you made pushed Windows into a weird boot state. Now, you can debug both with direct serial-based access and fix these issues with the tiniest of effort. It's like having a keyboard plugged into the server in our datacenter but in the comfort of your office or home.


### ![Setup a Serial Console access for Windows VM][activity] 2.15.1 Setup a Serial Console access for Windows VM

1. Sign into the Azure portal using the same account you activated the Windows VM with.

1. Open the Windows Virtual Machine and select the Serial Console menu.

1. If you are prompted to enable/configure boot diagnostics, click the link and turn it on. 

![Serial Console](../images/SerialConsole-1.png)

4. Type ? and press enter to get a list of the commands.

5. Type cmd and press enter and notice the **Cmd Channel** response.

6. Type ch -sn **Cmd Channel** and press enter.

7. Enter your username and the password you set when you created this VM with a blank domain.

![Serial Console](../images/SerialConsole-2.png)

8. Now you can execute any commands independent of network or OS state.

![Serial Console](../images/SerialConsole-3.png)

## Available in Linux or Windows
* Bash, CMD, Powershell, NMI, SysRq, vi, GRUB, etc.


### ![Reading][reading] Further Reading

These additional resources are also useful.

* [Virtual Machine Serial Console access](https://azure.microsoft.com/en-us/blog/virtual-machine-serial-console-access/)


[activity]: ../icons/activity.png "Workshop Activity!"
[discussion]: ../icons/discussion.png "Team Discussion!"
[reading]: ../icons/reading.png "Further Reading!"
