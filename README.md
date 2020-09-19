## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Detail image of Azure cloud network](https://github.com/RJack715/elk-stack-configuration/blob/master/Diagrams/Cloud_Server_Configuration.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

\'
---
 - name: metricbeat && filebeat
   hosts: webservers
   become: true
   tasks:
   - name: Download metricbeat deb
     command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

   - name: Download firebeat deb
     command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

   - name: Install metricbeat deb file
     command: dpkg -i metricbeat-7.4.0-amd64.deb

   - name: Install filebeat deb file
     command: dpkg -i filebeat-7.4.0-amd64.deb

   - name: Copy Metricbeat configuration file
     copy:
       owner: root
       src: /etc/ansible/metricbeat_configuration.yml
       dest: /etc/metricbeat/metricbeat.yml
       mode: '0644'

   - name: Copy Filebeat configuration file
     copy:
       owner: root
       src: /etc/ansible/filebeat-configuration.yml
       dest: /etc/filebeat/filebeat.yml
       mode: '0644'

   - name: Run metricbeat modules
     command: metricbeat modules enable docker

   - name: Run filebeat commands
     command: filebeat modules enable system

   - name: metricbeat setup
     command: metricbeat setup

   - name: filebeat setup
     command: filebeat setup

   - name: start metricbeat logs
     command: service metricbeat start

   - name: start filebeat logs
     command: service filebeat start
\'
This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the files and system statistics.

The configuration details of each machine may be found below.

| Name      | Function    | IP Address | Operating System |
|-----------|-------------|------------|------------------|
| Jump Box  | Gateway     | 10.0.0.8   | Linux            |
| Web-1     | DVWA Server | 10.0.0.6   | Linux            |
| Web-2     | DVWA Server | 10.0.0.7   | Linux            |
| ELK-Server| ELK Server  | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 98.199.135.69

Machines within the network can only be accessed by the Jump Box with 10.0.0.8 address.

A summary of the access policies in place can be found in the table below.

| Name      | Publicly Accessible | Allowed IP Addresses |
|-----------|---------------------|----------------------|
| Jump Box  | Yes                 | 98.199.135.69        |
| Web-1     | Yes                 | 98.199.135.69        |
| Web-2     | Yes                 | 98.199.135.69        |
| Elk-Server| Yes                 | 98.199.135.69        |

### Elk Configuration

Ansible was used to automate the configuration of the ELK machine. No configuration was performed manually, which is advantageous because of the need to configure multiple machines at once with lower chances of human error.

The playbook implements the following tasks:
- Downloads and Installs Docker and Python Software
- Downloads the Docker image to create the container
- Configures the container with published port mappings
- Start the container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Image of the newly created docker container](https://rice.bootcampcontent.com/RJ715/elk-stack-configuration/blob/master/Linux/ELK_Container.PNG)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1, IP: 10.0.0.6
- Web-2, IP: 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat monitors and sends the log files from the webserver to logstash . Metricbeat takes the metrics and statistics logs from the webservers and also sends them to Logstash

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the specific Beats configuration files to the Webservers by editing the Beats-Installation-playbook.yml to specify which webserver to install the beats
- Update the configuration file beforehand to include the ELK server IP address and port number
- Run the playbook, and navigate to Kibana using the link http://<ELK.VM.External.IP>:5601/app/kibana to check that the installation worked as expected.

### Specific Commands

[Beats installation file](https://rice.bootcampcontent.com/RJ715/elk-stack-configuration/blob/master/Ansible/Beats-Installation-playbook.yml)

ssh azdmin@104.214.99.248
- ssh to Jump-Box-Provisioner

sudo docker start funny_diffie
sudo docker attach funny_diffie
- start the ansible container

cd /etc/ansible
- where to find the configuration and playbook files 

nano Beats-Installation-playbook.yml
- to update the playbook to add beats to the webservers

nano /etc/ansible/ansible.cfg
nano /etc/ansible/hosts
- to add a webserver with its IP address to the ansible provisioner for automatic configurations

ssh azdmin@10.1.0.4
- ssh connection inside the ansible container to access the ELK server

(https://rice.bootcampcontent.com/RJ715/elk-stack-configuration/blob/master/Linux/filebeat_configuration.yml)
(https://rice.bootcampcontent.com/RJ715/elk-stack-configuration/blob/master/Linux/metricbeat_configuration.yml)
- configuration files to copy onto the ELK server and the VMs with updated IP address inside
