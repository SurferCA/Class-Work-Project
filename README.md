![alt text](https://github.com/SurferCA/Class-Work-Project/blob/main/Diagrams/Azure_Network.png)

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly *available*, in addition to restricting *access* to the network.
- **What aspect of security do load balancers protect?**
    - *Load balancers* enable you to run highly available services behind a private IP address which is accessible only within a cloud service or Virtual Network (VNet), giving additional security on that endpoint.
- **What is the advantage of a jump box?** 
    - Using a *JumpBox* in your network will allow controlled access and the ability to manage devices in a seperate security zone. The JumpBox is hardened and provides controlled means of access to other VM's through RDP, or SSH through port 22 (SSH was configured for this environment).

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the *data* and *system logs.*

  - Filebeat is used to collect log files from very specific files, such as logs generated by Apache, Microsoft Azure tools, the Nginx web server, or MySQL databases.
  - Metric beat is used to record and collect metrics from the operating system and from services running on the server.

The configuration details of each machine may be found below.

| Name     | Function | IP Address |    Operating System     |
|----------|----------|------------|-------------------------|
| Jump Box |Gateway   | 10.0.0.4   | Linux Ubuntu 18.04-LTS  |
| Web1 VM  |Webserver | 10.0.0.8   | Linux Ubuntu 18.04-LTS  |
| Web2 VM  |Webserver | 10.0.0.9   | Linux Ubuntu 18.04-LTS  |
| Web3 VM  |Webserver | 10.0.0.11  | Linux Ubuntu 18.04-LTS  |
| ELKStack |Logging   | 10.1.0.4   | Linux Ubuntu 18.04-LTS  |

### Access Policies

The virtual machines on the internal network are not exposed to the public Internet. 

Only the Jump Box VM machine can accept connections from the Internet. Access to this machine is only allowed from *My Personal workstation IP through private SSH key for security*.

- The following Machines within the network can only be accessed from the JumpBox IP 10.0.0.4 through SSH.
    - Web1, Web2, Web3 and ELK stack. 


A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible |    Allowed IP Addresses                                          |
|---------------|---------------------|------------------------------------------------------------------|
| Jump Box      | Yes, through SSH    | Personal IP, 10.1.0.4, 10.0.0.0/16                               |
| Load Balancer | HTTP Only           | Personal IP                                                      |
| Web1          | HTTP Only           | 10.0.0.4, 10.1.0.4,  Personal Public IP:port 80                  |
| Web2          | HTTP Only           | 10.0.0.4, 10.1.0.4,  Personal Public IP:port 80                  |
| Web3          | HTTP Only           | 10.0.0.4, 10.1.0.4,  Personal Public IP:port 80                  |
| ElkStack      | HTTP Only           | Personal IP, 10.1.0.4 (HTTP:5601), 10.0.0.4 (Jumpbox SSH)        |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
Ansible is an 'agent-less' configuration management system that simplifies the setup process for many different network systems. Ansible only needs to be installed on the 'control machine' then by using SSH, the configuration can be quickly and precisly implimented on any target(s) machine. You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure which makes Ansible automation a powerful and excellent choice for simplifing complex tasks.

*This playbook implements the following tasks:

- **Install docker.io** - The Docker engine, used for running containers.

- **Install python3-pip** - This is the package manager used to install Python Software.

- **Install docker** - (this is the Docker Python pip module)

- **Increase Virtual Memory** - This increases virtual memory in the Elk container.

- **Download and Launch Elk Docker Container** - This will download and start the sepb/elk:716 container on the Elk machine. Published ports include 5601:Kibana, 9200:Elastic Search, 5044:Filebeat.

- **Enable Docker System Service on Boot** - This tells Docker to enable on boot-up.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![alt text](https://github.com/SurferCA/Class-Work-Project/blob/main/Diagrams/elk_docker_ps1.png)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web_1VM 10.0.0.8 
- Web_2VM 10.0.0.9 
- Web_3VM 10.0.0.11

The following Beats have been installed on these machines:
- `Filebeat 7.6.2`
- `Metricbeat 7.6.1`

These Beats allow us to collect the following information from each machine:
- Filebeat- Monitors the Web-1VM, Web-2VM, Web-3VM machines and collects log events, systemwide file changes, and forwards them to Elasticsearch for indexing.
- Metricbeat- Periodically collect metrics from the operating system and information from the services running on the server.

### Using the Playbook
In order to use the playbook, you will need to have an *Ansible control node* already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat configuration file to */etc/ansible/files/filebeat-config.yml* 
- Update the /etc/ansible/hosts file to include *[elk] 10.1.0.4 ansible_python_interpreter=/usr/bin/python3*
- Run the playbook, and navigate to ELK GUI *http://10.1.0.4:5601/app/kibana* to check that the installation worked as expected. 
   -Navigate to Module Status
   -Click on Check Data
   -Scroll to bottom and click on Verify Incoming Data

### FileBeat 
- *Which file is the playbook?* filebeat-playbook.yml 
- *Where do you copy it?* on the Ansible controle node at: /etc/ansible/roles/filebeat-playbook.yml
- *Which file do you update to make Ansible run the playbook on a specific machine?* Ansible host file at /etc/ansible/hosts to configure the groups and playbook file to push out to a specific ip.
- *How do I specify which machine to install the ELK server on versus which to install Filebeat on?* 
   - First, both [elk] and [webservers] IP addresses need to be added to the Ansible Control node host file /etc/ansible/hosts 
   - Second, the playbook instructions will specify which machine to install to based on the host groups.
- *Which URL do you navigate to in order to check that the ELK server is running?* http://10.1.0.4:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

**Installing FileBeat**

- From the JumpBox, start and attach to Ansible Control Node:
   - **sudo docker start 02c5de3e7ef7**
   - **sudo docker attach 02c5de3e7ef7**
- Download template filebeat-config.yml to /etc/ansible/files
  - run: **curl       https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat >> /etc/ansible/filebeat-config.yml**
- Edit Filebeat Configuration File template to include the IP address of ELK machine and port numbers 
 - run: **nano /etc/ansible/files/filebeat-config.yml**
   - Edit line 1106 to include ELK IP & port- *hosts: ["10.1.0.4:9200"]*
   - Edit line 1806 to include ELK Ip & port- *host: "10.1.0.4:5601"*
- Create Ansible playbook installation instructions
  - run: **nano /etc/ansible/roles/filebeat-playbook.yml**
- Run Ansible playbook to install Filebeat on DVWA machines
  - run: **ansible-playbook filebeat-playbook.yml**
- Navigate to *http://10.1.0.4:5601/app/kibana/home*
- Click **Module Status** -Click on **Check Data** -Scroll to bottom and click on **Verify Incoming Data**

```
