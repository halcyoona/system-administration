In this blog I am going to show the process of installation of azure cli tool and then using this tool to create a ubuntu virtual machine and then accessing this virtual machine through ssh
and then add one user and then adding there priavte key and running one job in there evvironment.

You can install azure cli simple using the following command:
sudo apt install azure-cli

To use the Azure cli tool you need first to login into your account through terminal. For this purpose use following command:
az login
When you enter this you will be redirected to the browser, there you can enter your login detial. When you entered then you will get this type of message on your terminal

You have logged in. Now let us find all the subscriptions to which you have access...
The following tenants require Multi-Factor Authentication (MFA). Use 'az login --tenant TENANT_ID' to explicitly login to a tenant.
b76df79f-0110-465c-90f3-07df4642315d
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "4ea778976d-32bd-7f5f-aaa5-084cb61a0b0f",
    "id": "37623de4-e2df-4d06-8754-bd88314b4bd0",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Azure for Students",
    "state": "Enabled",
    "tenantId": "5ea9899d-30bd-4f6f-aaa5-084cb61a0b0f",
    "user": {
      "name": "halcyoona@gmail.com",
      "type": "user"
    }
  }
]


Now you are logged in and you can use Azure cli tool to create resources from cli.
az group create --name halcyoona-group --location eastus
{
  "id": "/subscriptions/95623de4-e1df-4c06-9954-bd66314b4bd0/resourceGroups/halcyoona-group",
  "location": "eastus",
  "managedBy": null,
  "name": "halcyoona-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}

In Azure, all resources are allocated in a resource management group. Resource groups provide logical groupings of resources that make them easier to work with as a collection.
You have to set the name of the resource and then location. Now resource group is set then move to next creating virtual machine with this resource. 

From now We look forward to creating a virtual machine using the Azure cli. 
az vm create --resource-group halcyoona-group --name halcyoonaVM --image UbuntuLTS --generate-ssh-keys --output json --verbose 
In this you have to set the name of the virtual machine. Second Image you want in your machine. Generate ssh key to access it without password using your private key and if you do not specify key name, it will create a key in ~/.ssh/id_rsa.pub by default. Output will be in json form.

Use existing SSH public key file: /home/halcyoona/.ssh/id_rsa.pub
Succeeded: Microsoft.Compute (None)
Accepted: vm_deploy_KTj2l8zpcfIt4I6cdv7bnjFNdYt40Yjh (Microsoft.Resources/deployments)
Succeeded: halcyoonaVMVNET (Microsoft.Network/virtualNetworks)
Succeeded: halcyoonaVMPublicIP (Microsoft.Network/publicIPAddresses)
Succeeded: halcyoonaVMNSG (Microsoft.Network/networkSecurityGroups)
Succeeded: vm_deploy_KTj2l8zpcfIt4I6cdv7bnjFNdYt40Yjh (Microsoft.Resources/deployments)
Accepted: halcyoonaVM (Microsoft.Compute/virtualMachines)
Accepted: halcyoonaVM_disk1_1fd54628b6274337a36197ae5e210878 (Microsoft.Compute/disks)
Succeeded: halcyoonaVMVMNic (Microsoft.Network/networkInterfaces)
{- Finished ..
  "fqdns": "",
  "id": "/subscriptions/95623de4-e1df-4c06-9954-bd66314b4bd0/resourceGroups/halcyoona-group/providers/Microsoft.Compute/virtualMachines/halcyoonaVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-9E-6E-B7",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.114.31.5",
  "resourceGroup": "halcyoona-group",
  "zones": ""
}
Command ran in 236.272 seconds (init: 0.037, invoke: 236.235)

Now you can see different information in output. You always look into the information that is provided in the output. You can see publicIpAddress, you have to remember this for future to connect to your virtual machine.


Now virtual machine is created and then login to your machine using your private key.
ssh halcyoona@40.114.31.5

You will get this output.
The authenticity of host '40.114.31.5 (40.114.31.5)' can't be established.
ECDSA key fingerprint is SHA256:eWH1kWiz3UPOCLCg7BSgIwKh2WIy7lCzZ74D6X08iaw.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '40.114.31.5' (ECDSA) to the list of known hosts.
Connection closed by 40.114.31.5 port 22

You have not opened ssh port.using following command you can open the port 22.

az vm open-port --resource-group halcyoona-group --name halcyoonaVM --port 22

Now again use the same command. now you will login into your account.
ssh halcyoona@40.114.31.5

Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.3.0-1032-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jul 14 05:49:35 UTC 2020

  System load:  0.08              Processes:           108
  Usage of /:   4.4% of 28.90GB   Users logged in:     0
  Memory usage: 7%                IP address for eth0: 10.0.0.4
  Swap usage:   0%

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Now run the following command.
sudo apt update

Move to adding user in virtual machine. Following command is used to add user.
sudo useradd -m -s /bin/bash aztest

Now check the user using below command.
ls /home/

Now set the password for aztest:
sudo passwd aztest

To check password of the current users.
sudo vi /etc/shadow

Now exit from the root user and connect it using aztest
ssh aztest@40.114.31.5
aztest@40.114.31.5: Permission denied (publickey).
You will found an error permission denied. So add some setting for aztest using root user.

ssh halcyoona@40.114.31.5

Now open the file sshd_config and edit the file.
sudo vi /etc/ssh/sshd_config

When you open up sshd_config file you have to edit yes.
PasswordAuthentication Yes

Restart the sshd to get the effect the changes you have done in the file.
sudo service sshd restart
exit

Now login to your new user with the password you have created.
ssh aztest@40.114.31.5
whoami


Now execute your code on ubuntu.

How to run your code on background. suppose you have python code.

python file.py > file.log &

"&" give parent process to do not wait signal for the child process and just move on to the next so terminal comeback and go to next command.

nohup python file.py > file.log &
now "nohup" is used for to donot kill the child when parent is killed in this case parent is terminal when terminal is killed this wasn't killed.

You can see your logs in log file.
tail -f file.log


You can see your process is running.
ps aux | grep file

To kill the process.
kill -9 ID

Passwordless communication the virtual machine
Now create public private key using this command
ssh-keygen -f aztest

adding public to your Azure ubuntu machine
ssh-copy-id -i "aztest.pub" aztest@40.114.31.5

You will get this output.

/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "aztest.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
aztest@40.114.31.5's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'aztest@40.114.31.5'"
and check to make sure that only the key(s) you wanted were added.


ssh -i "aztest" aztest.13.92.187.153

to check authorized keys
vi ~/.ssh/authorized_keys
