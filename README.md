✅ Project Documentation: Jenkins Freestyle Job to Trigger Ansible Playbook
✅ 1. Project Overview

This project demonstrates how to automate configuration management using:

Jenkins Freestyle Job

GitHub repository

Ansible playbook

AWS EC2 instance (Ubuntu)

Whenever the Jenkins job runs, it:

Pulls Ansible code from GitHub

Connects to the remote EC2 instance via SSH

Executes the Ansible playbook

Installs Apache on the target server

This is a complete CI/CD style automation using Jenkins + Ansible.

✅ 2. Architecture Diagram (Text Form)
Jenkins Server  → GitHub Repo  → Ansible Playbook  → AWS EC2 Instance
      |                |                 |                   |
   Clone code       Inventory          SSH Key           Apache installed
                    Playbook           Login

✅ 3. Environment Setup
✅ On Jenkins Server:

Install Ansible & Git:

sudo apt update
sudo apt install ansible git -y


Ensure Jenkins user has permissions:

sudo visudo
jenkins ALL=(ALL) NOPASSWD: ALL

✅ 4. SSH Key Setup (Important)
✅ Switch to Jenkins user:
sudo su - jenkins

✅ Generate SSH key:
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

✅ Copy the key to the EC2 instance:
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@13.201.81.232


(If ssh-copy-id fails on EC2, manually paste key into authorized_keys)

✅ 5. GitHub Repository Structure

Your repo should contain:

ansible-project/
 ├── inventory
 └── playbook.yml

✅ Inventory file

This tells Ansible how to log into the EC2 machine.

[all]
13.201.81.232 ansible_user=ubuntu ansible_ssh_private_key_file=/var/lib/jenkins/.ssh/id_rsa

✅ Playbook file
---
- hosts: all
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

✅ 6. Create Jenkins Freestyle Project
Step 1:

Go to Jenkins Dashboard → New Item

Name: ansible-freestyle

Select: Freestyle Project

✅ 7. Configure Git in Jenkins

Under Source Code Management → Git

Repository URL:

https://github.com/YOUR_USERNAME/ansible-project.git


Branch:

*/main

✅ 8. Add Build Step in Jenkins

Go to:

Build → Add build step → Execute shell

Paste:

echo "Running Ansible playbook from Jenkins..."

cd $WORKSPACE

ansible-playbook -i inventory playbook.yml


(Optional: skip host key checking)

ansible-playbook -i inventory playbook.yml --ssh-common-args='-o StrictHostKeyChecking=no'


Save the job.

✅ 9. Run the Jenkins Job

Click:

✅ Build Now

Open:

✅ Build → Console Output

Successful output includes:

ok: [13.201.81.232]
changed: [13.201.81.232]
PLAY RECAP
ok=2    changed=1


Apache is now installed on the EC2 instance.

✅ 10. Verification

SSH into your EC2:

ssh ubuntu@13.201.81.232


Check Apache:

sudo systemctl status apache2


Or open the EC2 Public IP:

http://13.201.81.232


You should see the Apache default page.

✅ 11. Final Summary (Copy for Resume)

Created a Jenkins Freestyle Job integrated with GitHub to automate infrastructure configuration using Ansible. The Jenkins pipeline cloned the Ansible repository, connected to an AWS EC2 instance using SSH key authentication, and executed an Ansible playbook to install and configure Apache. Successfully implemented secure key-based access, Ansible inventory management, and automated provisioning using Jenkins.

✅ Want me to convert this into a PDF, README.md, or a full project report?
