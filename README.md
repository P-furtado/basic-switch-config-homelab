# Basic LAN Setup Homelab

## Objectives
- Simulate a console connection to a switch using Packet Tracer.
- Understand and configure basic switch settings.
- Learn to configure hostname, IP addresses, MOTD, and passwords.
- Practice using show commands to verify switch configurations.

## Software Requirements
- Cisco Packet Tracer

---

## Lab Setup
![Lab Topology Screenshot](assets/basic-lan-setup-topology.png)  
*(Insert your Packet Tracer topology screenshot here — save the image in an `assets/` folder in your repo.)*

---

## Part 1: Simulating the Console Connection Using Packet Tracer

### Step 1: Open Cisco Packet Tracer and Create a Topology
- Launch Packet Tracer.  
- Add the following devices to your workspace:  
  - One Cisco 2960 Switch  
  - One PC  
  - One Console Cable  

### Step 2: Connect the PC to the Switch Using a Console Cable
- In Packet Tracer, select the **Console Cable** from the cable options.  
- Click on the PC and connect the cable to the **RS-232 port** (console port).  
- Click on the Switch and connect the other end to the **Console Port**.  

### Step 3: Access the Switch via the Terminal on the PC
- Click on the **PC**.  
- Go to the **Desktop** tab and click on the **Terminal** application.  
- Configure the terminal settings (use default values: *9600 Baud rate, 8 data bits, no parity, 1 stop bit*).  
- You should now have access to the switch’s **CLI (Command Line Interface)**.  

## Part 2: Verify the Default Switch Configuration

In this step, you will examine the default switch settings, such as the current switch configuration, IOS information, interface properties, VLAN information, and flash memory.

**Switch> enable**  

This command allows you to access **Privileged EXEC Mode**, where you can execute higher-level commands. By default, the switch starts in **User EXEC Mode** with limited access.
Follow these steps to verify the switch configuration:

1. Examine the current running configuration file:  
   **Switch# show running-config**

2. Examine the startup configuration file in NVRAM:  
   **Switch# show startup-config**

3. Examine the Cisco IOS version information of the switch:  
   **Switch# show version**

4. Examine the default VLAN settings of the switch:  
   **Switch# show vlan**

5. Examine flash memory:  
   **Switch# show flash**

   ## Part 3: Configure Basic Network Device Settings

Configure basic switch settings, including hostname, local passwords, and MOTD banner. Follow these steps:

Step 1: Assign the Switch Hostname
Assign a unique hostname to the switch to make it easier to identify on the network.
- Command:
  - Switch(config)# hostname Switch01

Step 2: Enable Password Encryption
Encrypt all plain-text passwords to add a layer of security.
- Command:
  - Switch01(config)# service password-encryption

Step 3: Set Privileged EXEC Mode Password
You can configure a secret password for privileged EXEC mode to make sure only authorized users can access high-level commands.
- Command:
  - Switch01(config)# enable secret class

Step 4: Prevent Unwanted DNS Lookups
Disable DNS lookups to stop the switch from trying to resolve incorrectly typed commands as hostnames, which can delay command execution.
- Command:
  - Switch01(config)# no ip domain-lookup

Step 5: Configure a Message of the Day (MOTD) Banner
Set a banner to warn unauthorized users whenever they access the switch.
- Commands:
  - Switch01(config)# banner motd #
  - Enter Text message. End with the character ‘#’.
  - Unauthorized access is strictly prohibited. #

Step 6: Secure the Console Port
Restrict console access and prevent console messages from interrupting commands. By default, all console connections are allowed with no password.
- Commands:
  - Switch01(config)# line con 0
  - Switch01(config-line)# password cisco
  - Switch01(config-line)# login
  - Switch01(config-line)# logging synchronous
  - Switch01(config-line)# exit

## Part 4: Configure an IP Address on VLAN 1 & Default Gateway
Step 1: Configure an IP Address on VLAN 1

- Commands:
  - Switch01(config)# interface vlan 1
  - Switch01(config-if)# ip address 192.168.1.5 255.255.255.0
  - Switch01(config-if)# no shutdown
  VLAN 1 is the default VLAN on most switches. Assigning an IP address to VLAN 1 allows you to remotely manage the switch. The `no shutdown` command ensures the interface is activated.

Step 2: Configure a Default Gateway

- Command:
  - Switch01(config)# ip default-gateway 192.168.1.1
The default gateway allows the switch to communicate with devices outside its local network, enabling remote management from different subnets.

## Part 5:Part 5. Enable Remote Management SSH (more secure): 
tep 1: Create a Local User
Create a local user with administrative privileges for SSH login.
- Command:
  - Switch01(config)# username admin privilege 15 secret cisco

Step 2: Create a Domain Name
A domain name is required to generate RSA keys for SSH.
- Command:
  - Switch01(config)# ip domain-name eastcharmer.com

Step 3: Generate RSA Keys for SSH
Generate the RSA keys needed for secure SSH communication.
- Commands:
  - Switch01(config)# crypto key generate rsa
  - When prompted, choose a key modulus size (e.g., 1024 bits). Example output:
    ```
    The name for the keys will be: Switch01.eastcharmer.com
    How many bits in the modulus [512]: 1024
    % Generating 1024 bit RSA keys, keys will be non-exportable... [OK]
    ```

Step 4: Enable SSH Version 2 and Configure VTY Lines
Enable SSH access and restrict remote access to SSH only using local login credentials.
- Commands:
  - Switch01(config)# ip ssh version 2
  - Switch01(config)# line vty 0 15
  - Switch01(config-line)# transport input ssh
  - Switch01(config-line)# login local

Step 5: Test Remote SSH Access
From a PC on the same network, open the Command Prompt and test SSH access to the switch.
- Command:
  - PC> ssh -l admin 192.168.1.5
  - Enter the password (`cisco`) when prompted to log in securely.

Step 6: Verify SSH Configuration
Check the switch’s interface status and SSH configuration.
- Commands:
  - Switch# show ip interface brief
  - Switch# show running-config






