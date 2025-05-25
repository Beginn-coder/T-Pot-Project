# Overview
Creating a honeypot has been on my list of projects to complete since I started building my cybersecurity portfolio. After seeing an article by Tyler Wall, I decided to revisit this project and this time, let the honeypot run for 24 hours to see what happens. 

# Explanation
If you're not familiar with what a honeypot is, here's a quick description. A honeypot is designed to attract and trap hackers, allowing security teams to study their behavior and tactics. Think of it as a decoy house in a neighborhood. Burglars might try to break in, thinking it’s an easy target, but instead, they get caught in the act. That isn't to say that honeypots aren't without their drawbacks as they are resource intensive, have a limited scope, and can potentially lead to the protected system becoming compromised if the honeypot hasn't been properly configured. 

# Setup
### Part 1
The first thing you'll want to do is head to the [Azure Portal](http://portal.azure.com/) and create an account. Once done, you'll get a $200 credit to use for your projects over 30 days or until the credit runs out. Now search up virtual machines, click on the service, and you'll be brought to the screen shown below ![image](https://github.com/user-attachments/assets/1e1cc1d4-1bf2-49f5-903a-284f881905b0)

Click create and you should see a page similar to this:![image](https://github.com/user-attachments/assets/8018c468-8ca6-4f03-81ad-97e762d8d16c)

Click on 'Create New' to create a resource group. You can name it whatever you like or you can call it 'tpot-rg' for simplicity.

**Note: **A resource is the individual service that you will be consuming, and a resource group is a group of these resources together.****

This project has a few resources such as the Virtual Machine, Public IP address, Network Security Group,… etc., inside this resource group. Once you're finished with the lab, just delete the resource group to delete this entire project.
![image](https://github.com/user-attachments/assets/2b097a05-adb1-4bcc-8beb-7c683ad1ac7a)

- Give your virtual machine a name such as "tpot-vm"
- Set the region(for my project, I used US East 2)
- Set No Infrastructure Redundancy Required for the Availability.
- For the image, use any of the supported images for T-Pot(I used Ubuntu 24.04 LTS)

![image](https://github.com/user-attachments/assets/81c23dad-a670-4d70-99da-2fcbb7e394b8)

- Choose size: “Standard_B4ms - 4 vcpus 16GiB's memory"

![image](https://github.com/user-attachments/assets/80cb0d39-576b-461e-86bf-e647d3da4acb)
I used an ssh key instead of a password 

Now move onto disks and give the virtual machine 256 GiB's of space. I found that in previous tests 128 GiB's of storage wouldn't allow the ip:64297 to load. 
![image](https://github.com/user-attachments/assets/ff2324f7-3bc9-4d8c-8d1a-0b6d028a3ba8)

Moving onto networking, make sure to check the box to delete the public IP and NIC when the VM is deleted. Everything else can be left as is. 
![image](https://github.com/user-attachments/assets/f53ca026-92f5-450d-9b7f-0b944d153ab7)

Once this is done, you can move onto Review+Create where Azure will run a validation test. If the validation test passes, you can click on 'Create'. You'll also receive a notice to download the ssh key which you'll want to do.


### Part 2
Now that you've created the virtual machine, it's time to open the floodgates. In the search bar at the top of the screen, type in (VM's name)-vm-nsg and select the network security group resource

![image](https://github.com/user-attachments/assets/06c2fd9f-dd9a-4600-8394-bd0a56ba4595)

Once in, under settings, select Inbound Security Rules and click add
![image](https://github.com/user-attachments/assets/119ceb63-41af-4834-80fd-727797a21072)

- Change Destination port ranges to “*”
- Change Source port ranges to "*"
- Change Priority to “100”
- Give it a name and click add

![image](https://github.com/user-attachments/assets/1d4c71b4-8259-4b1a-9c7f-7a0db4b1e500)


# Part 3
Download and install the PuTTY application if you're on windows. Once done, open PuTTYGen and select "Conversions -> Import Key". Select the .pem file that you downloaded earlier and click 'save private key' 
![image](https://github.com/user-attachments/assets/19e5b85f-1251-4083-9287-7586ebbec85b)

You'll be prompted to save the key in a folder of your choice. Now head to PuTTY, copy the public ip of your vm, paste it into the hostname section and under SSH-> Auth-> Credentials, select the private key that you generated and click 'open'. 

Your session window will pop up and enter your generated username. Now execute the following commands
- env bash -c "$(curl -sL https://github.com/telekom-security/tpotce/raw/master/install.sh)"
- Select "Hive" or h install
- sudo reboot (when finished)
-
**Note: you will be prompted to create a username and password for T-Pot during the hive install process**

Once done, in a web browser of your choice, go to http://<your VM's IP>:64297 and enter in the user credentials that you created during the T-pot installation. 
You'll see a landing page like the one shown below
![image](https://github.com/user-attachments/assets/19b455dd-fa34-4f2b-8985-9964f42533e7)

Now you can look at the attack map and see where the hits are coming from or check out Kibana and see how many attacks are coming in such as the example below
![image](https://github.com/user-attachments/assets/7a2e6e25-983e-4afd-a927-271b3f758d46)
