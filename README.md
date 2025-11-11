# ansible
A small project for Automate the installation and startup of the Nginx web server on a Target EC2 instance using Ansible from a Source (Control) EC2 instance with ansible playbook

Steps to follow -

1.Set Up Source and Target Servers (EC2 Instances)

        Created two Ubuntu EC2 instances on AWS:
        
        Source Server → Control node where Ansible is installed
        
        Target Server → Managed node where Nginx will be installed
        
        Both servers must have SSH connectivity and public/private IPs.

2.Install Ansible on Source Server

    Update and install Ansible on the Source EC2 instance (Ubuntu):
    
        sudo apt update 
        sudo apt install ansible 
    
    Verify installation:
    
        ansible --version
    
    This confirms Ansible is properly installed and ready to use.


3️. Configure Passwordless SSH Authentication

    Generate SSH key pair on the Source server:
    
        ssh-keygen
    
    Copy the public key to the Target server:
    
    ssh-copy-id ubuntu@<target-private-ip>
    
    (or manually paste the contents of ~/.ssh/id_rsa.pub into /home/ubuntu/.ssh/authorized_keys on the Target server)
    
    Test SSH connection:
    
    ssh ubuntu@<target-private-ip>
    
    If it connects without asking for a password, authentication is configured correctly.

4️. Verify Ansible Connection (Adhoc Command)

    Use Ansible Adhoc command to ping the Target server:
    
    ansible all -i <target-private-ip>, -u ubuntu -m ping
    
    
    You should see a “pong” response.
    
    Example to create a test file on Target:
    
    ansible all -i <target-private-ip>, -u ubuntu -m file -a "path=/home/ubuntu/testfile.txt state=touch"
    
    
    Check on Target server:
    
    ls /home/ubuntu/testfile.txt
    
    
    confirms Ansible can execute remote tasks successfully.

5️. Create and Configure the Inventory File

    Create an inventory file to group managed servers.
    
    vim inventory
    
    
    Example content:
    
    [webservers]
    <target-private-ip> ansible_user=ubuntu
    
    [Databases]
    <target-private-ip> ansible_user=ubuntu
    
    Save the file and verify inventory:
    
    ansible-inventory -i inventory --list
    
    Grouping servers simplifies multi-server management.

6️. Create and Run Ansible Playbook for Nginx

    Create a playbook file:
    
    vim nginx_playbook.yml
    
    
    Example playbook content:
    
    ---
    - name: Install and Start Nginx Web Server
      hosts: webservers
      become: true
      tasks:
        - name: Update apt packages
          apt:
            update_cache: yes
    
        - name: Install Nginx
          apt:
            name: nginx
            state: present
    
        - name: Start Nginx service
          service:
            name: nginx
            state: started
    
    
    Run the playbook:
    
    ansible-playbook -i inventory nginx_playbook.yml
    
    This installs and starts Nginx on the Target server automatically.

7️. Verify Nginx Installation

    SSH into the Target server and confirm Nginx is active:
    
    systemctl status nginx
    
    Also, test from a browser using the Target server’s public IP:
    
    http://<target-public-ip>
    
    You should see the default Nginx welcome page.

🎯 Conclusion

    Successfully automated Nginx installation and startup using Ansible.
    
    Demonstrated:
    
    Passwordless SSH setup
    
    Adhoc commands
    
    Inventory grouping
    
    Playbook automation
    
    All tasks executed successfully — Green across the board.
    <img width="1351" height="755" alt="Screenshot 2025-11-11 102843" src="https://github.com/user-attachments/assets/032052aa-ad62-4878-8253-1b59fe6fa896" />

    


