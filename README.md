# Ansible Playbook for hosting a WordPress website on different OS'- AmazonLinux, Ubuntu with LAMP stack.

As you may all know, making a WordPress website live with LAMP stack is a lot of manual work and think if you also have to do it on different Operating Systems!! 

In this repo, I will show you how to use Ansible to automate the installation of WordPress with the LAMP stack running on an Amazon Linux and Ubuntu server at a time. This is done in order to minimize manual intervention. WordPress is used to create blogs and websites utilizing PHP as coding language and MySQL/MariaDB database to store data. Once the WordPress installed, users can administer their websites using the web admin panel.

## Core Features:
- A Wordpress website will be automatically installed on all the client servers.
- Tasks and variables are included separately on the main YAML file.
- MySQL root password will be prompted on every run of the playbook.

## Table of Contents:

- [Prerequisites](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#prerequisites)
- [Playbook Actions Summary](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#playbook-actions-summary)
- [Customize the Variables](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#customize-the-variables)
- [Syntax check](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#syntax-check)
- [Executing the Playbook](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#executing-the-playbook)
- [Output](https://github.com/winifredkj/Ansible-WordPress_LAMP_on_AmazonLinux_and_Ubuntu/blob/main/README.md#output)

## Prerequisites:
- We are using AWS EC2 Instances to work on this project. So, you must have a working AWS account.
- Since this repo is based on both Amazon Linux and Ubuntu operating systems, you will need to have one Amazon Linux and one Ubuntu EC2 instances running as client servers and another AmazonLinux Ec2 instance running as master server.
- An [Amazon EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the working directory with proper permission. 
- A security group that allows SSH (Secure Shell), Database (MariaDB) and HTTPS access
- Ansible must be installed on the master server. You can use the following command to install ansible on the server. The ansible version I am using on the master server is 'ansible 2.9.23'.
   ```
   $ sudo amazon-linux-extras install ansible2 -y
- An [inventory file](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#example-one-inventory-per-environment) in the working directory that contains the SSH details of the client servers. Here, my inventory file name is "hosts". Here, I am using the OS names to group the servers in the inventory. I have named the groups as "amazon" and "ubuntu" for the AmazonLinux and Ubuntu instances respectively.
  ``` 
     [amazon]
     privateIP ansible_user="ec2-user" ansible_port="22" ansible_ssh_private_key_file="aws.pem"
     [ubuntu]
     privateIP ansible_user="ubuntu" ansible_port="22" ansible_ssh_private_key_file="aws.pem" 

     where "privateIP" will be the private IPv4 address assigned to the EC2 instances and "aws.pem" is my EC2 key.  
- Confirm SSH connectivity from master to client servers. You can use the following command for the same.
  ``` 
  ansible -i hosts all -m ping
## Playbook Actions Summary:
In this section, I will show you what this Ansible Playbook does when we execute it. 
- Include the variable files for AmazonLinux and Ubuntu in the main.yml file.
- Include the task files for AmazonLinux and Ubuntu in the main.yml file.
- Install "httpd" or "apache2" software for the webserver on AmazonLinux and Ubuntu clients respectively.
- Install "PHP" software and its modules for supporting the website content.
- For AmazonLinux - Copy the template file "httpd.conf.j2" for the main httpd configuraion from the master and creates the "/etc/httpd/conf/httpd.conf" on the client.
- For Ubuntu - Copy the template file "ports.conf.j2" that mention the apache listening port and create as "/etc/apache2/port.conf"
- VirtualHost configuration file creation - Copy the template file from master and create the configuration file on the clients.
- Create a sample test.html and info.php page to test the installation later if needed.
- Install MariaDB and MySQL-python as the database server and its dependant.
- Restart and Enable MariaDB service.
- Update the root password for mariadb as mentioned in the main.yml file. Note that after the first installation, there won't be any root password for the MariaDB server. You can just press "Enter" when it prompts on the first time.
- Remove anonymous MariaDB users.
- Create WordPress Database.
- Create a Wordpress database user with password and assign all the privilleges for that user on that database.
- Download the latest Wordpress archive file from the official WordPress website.
- Extract the Zip file at the client server.
- Copy the extracted WordPress files to the Document root of the webserver.
- WordPress configuration file creation - Copy the template "wp-config.php.j2" and create "wp-config.php" file in the document root.
- Post Installation - Restart Apache and MariaDB services on the clients
- Post Installation - Remove the WordPress archive and extracte file in the temporary directory from the clients.
## Customize the Variables:
You can customize the variables used in the project by editing the 'default' values if needed.

- Listening port of the webserver: _apache_port_
- The domain name on which we install wordpress: _apache_domain_
- Owner of the file/directory in the apache configuration: _apache_owner_
- Group of the file/directory in the apache configuration: _apache_group_
- MariaDB root password: _mariadb_root_pass_
- Wordpress User: _mariadb_xtra_user_
- Wordpress database: _mariadb_xtra_db_
- Wordpress database user password: _mariadb_xtra_pass_
- URL of the downloading latest archive file of WorPress: _wp_url_

## Syntax check:
``` 
$ ansible-playbook -i hosts main.yml --syntax-check

playbook: main.yml
```
## Executing the Playbook:
```
$ ansible-playbook -i hosts main.yml
```
## Output:
1st Run:
```
$ ansible-playbook -i hosts main.yml
Enter MariaDB root password:

PLAY [To host a WordPress website on different OS'] **************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************
ok: [172.31.43.234]
ok: [172.31.2.149]

TASK [include_vars] **********************************************************************************************************************************
ok: [172.31.2.149]
skipping: [172.31.43.234]

TASK [include_vars] **********************************************************************************************************************************
skipping: [172.31.2.149]
ok: [172.31.43.234]

TASK [include_tasks] *********************************************************************************************************************************
skipping: [172.31.43.234]
included: /home/ec2-user/myproject-wordpress/amazonlinux-wordpress-tasks.yml for 172.31.2.149

TASK [Apache - Installing Httpd] *********************************************************************************************************************
changed: [172.31.2.149]

TASK [Apache - install PHP support] ******************************************************************************************************************
changed: [172.31.2.149]

TASK [Apache - Creating httpd.conf from httpd.conf.j2] ***********************************************************************************************
ok: [172.31.2.149]

TASK [Apache - Creating VirtualHost from virtualhost.conf.j2 file] ***********************************************************************************
changed: [172.31.2.149]

TASK [Apache - Creating sample test.html and info.php page] ******************************************************************************************
changed: [172.31.2.149] => (item=test.html)
changed: [172.31.2.149] => (item=info.php)

TASK [MariaDB - Installing MariaDB and MySQL-python] *************************************************************************************************
changed: [172.31.2.149]

TASK [MariaDB - Restarting and Enabling service] *****************************************************************************************************
changed: [172.31.2.149]

TASK [MariaDB - updating root password for mariadb] **************************************************************************************************
[WARNING]: Module did not set no_log for update_password
changed: [172.31.2.149]

TASK [MariaDB - removing anonymous users] ************************************************************************************************************
changed: [172.31.2.149]

TASK [MariaDB - creating extra database: wpdatabase] *************************************************************************************************
changed: [172.31.2.149]

TASK [MariaDB - creating extra user: wpuser] *********************************************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Downloading WP archive https://wordpress.org/latest.zip] ***************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Extracting the Zip file] ***********************************************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Copying wp files from /tmp to /var/www/html/wp-amazon.devopsive.com/] **************************************************************
changed: [172.31.2.149]

TASK [WordPress - Creating wp-config.php from wp-config.php.j2] **************************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Post Installation - Restarting services] *******************************************************************************************
changed: [172.31.2.149] => (item=mariadb)
changed: [172.31.2.149] => (item=httpd)

TASK [WordPress - Post Installation - Cleanup] *******************************************************************************************************
changed: [172.31.2.149] => (item=/tmp/wordpress/)
changed: [172.31.2.149] => (item=/tmp/wordpress.zip)

TASK [include_tasks] *********************************************************************************************************************************
skipping: [172.31.2.149]
included: /home/ec2-user/myproject-wordpress/ubuntu-wordpress-tasks.yml for 172.31.43.234

TASK [Apache - Installing Httpd] *********************************************************************************************************************
changed: [172.31.43.234]

TASK [Apache - install PHP support] ******************************************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Restarting and Enabling Apache service] ***********************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating port.conf from port.conf.j2] *************************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating VirtualHost from wordpress.conf.j2 file] *************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating a symlink] *******************************************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating document root for wp-ubuntu.devopsive.com] ***********************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating sample test.html and info.php page] ******************************************************************************************
changed: [172.31.43.234] => (item=test.html)
changed: [172.31.43.234] => (item=info.php)

TASK [MariaDB - Install pip, unzip] ******************************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - Install PyMySQL using pip] ***********************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - Installing MariaDB] ******************************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - Restarting and Enabling service] *****************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - updating root password for mariadb] **************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - removing anonymous users] ************************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - creating extra database: wpdatabase] *************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - creating extra user: wpuser] *********************************************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Downloading WP archive https://wordpress.org/latest.zip] ***************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Extracting the Zip file] ***********************************************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Copying wp files from /tmp to /var/www/html/wp-ubuntu.devopsive.com/] **************************************************************
changed: [172.31.43.234]

TASK [WordPress - Creating wp-config.php from wp-config.php.j2] **************************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Post Installation - Restarting services] *******************************************************************************************
changed: [172.31.43.234] => (item=mariadb)
changed: [172.31.43.234] => (item=apache2)

TASK [WordPress - Post Installation - Cleanup] *******************************************************************************************************
changed: [172.31.43.234] => (item=/tmp/wordpress/)
changed: [172.31.43.234] => (item=/tmp/wordpress.zip)

PLAY RECAP *******************************************************************************************************************************************
172.31.2.149               : ok=20   changed=16   unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
172.31.43.234              : ok=25   changed=21   unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```
Re-run:
```
$ ansible-playbook -i hosts main.yml
Enter MariaDB root password:

PLAY [To host a WordPress website on different OS'] **************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************
ok: [172.31.43.234]
ok: [172.31.2.149]

TASK [include_vars] **********************************************************************************************************************************
ok: [172.31.2.149]
skipping: [172.31.43.234]

TASK [include_vars] **********************************************************************************************************************************
skipping: [172.31.2.149]
ok: [172.31.43.234]

TASK [include_tasks] *********************************************************************************************************************************
skipping: [172.31.43.234]
included: /home/ec2-user/myproject-wordpress/amazonlinux-wordpress-tasks.yml for 172.31.2.149

TASK [Apache - Installing Httpd] *********************************************************************************************************************
ok: [172.31.2.149]

TASK [Apache - install PHP support] ******************************************************************************************************************
changed: [172.31.2.149]

TASK [Apache - Creating httpd.conf from httpd.conf.j2] ***********************************************************************************************
ok: [172.31.2.149]

TASK [Apache - Creating VirtualHost from virtualhost.conf.j2 file] ***********************************************************************************
ok: [172.31.2.149]

TASK [Apache - Creating sample test.html and info.php page] ******************************************************************************************
ok: [172.31.2.149] => (item=test.html)
ok: [172.31.2.149] => (item=info.php)

TASK [MariaDB - Installing MariaDB and MySQL-python] *************************************************************************************************
ok: [172.31.2.149]

TASK [MariaDB - Restarting and Enabling service] *****************************************************************************************************
changed: [172.31.2.149]

TASK [MariaDB - updating root password for mariadb] **************************************************************************************************
[WARNING]: Module did not set no_log for update_password
ok: [172.31.2.149]

TASK [MariaDB - removing anonymous users] ************************************************************************************************************
ok: [172.31.2.149]

TASK [MariaDB - creating extra database: wpdatabase] *************************************************************************************************
ok: [172.31.2.149]

TASK [MariaDB - creating extra user: wpuser] *********************************************************************************************************
ok: [172.31.2.149]

TASK [WordPress - Downloading WP archive https://wordpress.org/latest.zip] ***************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Extracting the Zip file] ***********************************************************************************************************
changed: [172.31.2.149]

TASK [WordPress - Copying wp files from /tmp to /var/www/html/wp-amazon.devopsive.com/] **************************************************************
ok: [172.31.2.149]

TASK [WordPress - Creating wp-config.php from wp-config.php.j2] **************************************************************************************
ok: [172.31.2.149]

TASK [WordPress - Post Installation - Restarting services] *******************************************************************************************
changed: [172.31.2.149] => (item=mariadb)
changed: [172.31.2.149] => (item=httpd)

TASK [WordPress - Post Installation - Cleanup] *******************************************************************************************************
changed: [172.31.2.149] => (item=/tmp/wordpress/)
changed: [172.31.2.149] => (item=/tmp/wordpress.zip)

TASK [include_tasks] *********************************************************************************************************************************
skipping: [172.31.2.149]
included: /home/ec2-user/myproject-wordpress/ubuntu-wordpress-tasks.yml for 172.31.43.234

TASK [Apache - Installing Httpd] *********************************************************************************************************************
ok: [172.31.43.234]

TASK [Apache - install PHP support] ******************************************************************************************************************
ok: [172.31.43.234]

TASK [Apache - Restarting and Enabling Apache service] ***********************************************************************************************
changed: [172.31.43.234]

TASK [Apache - Creating port.conf from port.conf.j2] *************************************************************************************************
ok: [172.31.43.234]

TASK [Apache - Creating VirtualHost from wordpress.conf.j2 file] *************************************************************************************
ok: [172.31.43.234]

TASK [Apache - Creating a symlink] *******************************************************************************************************************
ok: [172.31.43.234]

TASK [Apache - Creating document root for wp-ubuntu.devopsive.com] ***********************************************************************************
ok: [172.31.43.234]

TASK [Apache - Creating sample test.html and info.php page] ******************************************************************************************
ok: [172.31.43.234] => (item=test.html)
ok: [172.31.43.234] => (item=info.php)

TASK [MariaDB - Install pip, unzip] ******************************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - Install PyMySQL using pip] ***********************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - Installing MariaDB] ******************************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - Restarting and Enabling service] *****************************************************************************************************
changed: [172.31.43.234]

TASK [MariaDB - updating root password for mariadb] **************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - removing anonymous users] ************************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - creating extra database: wpdatabase] *************************************************************************************************
ok: [172.31.43.234]

TASK [MariaDB - creating extra user: wpuser] *********************************************************************************************************
ok: [172.31.43.234]

TASK [WordPress - Downloading WP archive https://wordpress.org/latest.zip] ***************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Extracting the Zip file] ***********************************************************************************************************
changed: [172.31.43.234]

TASK [WordPress - Copying wp files from /tmp to /var/www/html/wp-ubuntu.devopsive.com/] **************************************************************
ok: [172.31.43.234]

TASK [WordPress - Creating wp-config.php from wp-config.php.j2] **************************************************************************************
ok: [172.31.43.234]

TASK [WordPress - Post Installation - Restarting services] *******************************************************************************************
changed: [172.31.43.234] => (item=mariadb)
changed: [172.31.43.234] => (item=apache2)

TASK [WordPress - Post Installation - Cleanup] *******************************************************************************************************
changed: [172.31.43.234] => (item=/tmp/wordpress/)
changed: [172.31.43.234] => (item=/tmp/wordpress.zip)

PLAY RECAP *******************************************************************************************************************************************
172.31.2.149               : ok=20   changed=6    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
172.31.43.234              : ok=25   changed=6    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```
