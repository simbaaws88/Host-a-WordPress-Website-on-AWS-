![Alt text](2._Host_a_WordPress_Website_on_AWS.png)
---
# Hosting a WordPress Website on AWS

## Overview

This project demonstrates how to host a WordPress website on AWS using a variety of AWS services to ensure high availability, scalability, and security. The infrastructure was designed to leverage AWS's capabilities to provide a robust and resilient web application deployment.

## Architecture

The architecture of the project includes the following components:

1. **VPC Configuration**: A Virtual Private Cloud (VPC) was configured with both public and private subnets across two different availability zones to enhance fault tolerance and high availability.
   
2. **Internet Gateway**: An Internet Gateway was deployed to allow connectivity between the VPC and the Internet.
   
3. **Security Groups**: Security Groups were established as a network firewall mechanism to control traffic to and from the web servers.
   
4. **Multi-AZ Deployment**: The infrastructure leveraged two Availability Zones to enhance system reliability and fault tolerance.
   
5. **Public Subnets**: Public Subnets were utilized for infrastructure components like the NAT Gateway and Application Load Balancer.
   
6. **EC2 Instance Connect Endpoint**: Implemented for secure connections to assets within both public and private subnets.
   
7. **Private Subnets**: Web servers (EC2 instances) were placed within Private Subnets for enhanced security.
   
8. **NAT Gateway**: Enabled instances in both the private Application and Data subnets to access the Internet via the NAT Gateway.
   
9. **EC2 Instances**: Hosted the WordPress website on EC2 Instances within private subnets.
   
10. **Application Load Balancer**: Employed an Application Load Balancer and a target group to distribute web traffic evenly to an Auto Scaling Group of EC2 instances across multiple Availability Zones.
   
11. **Auto Scaling Group**: Utilized an Auto Scaling Group to automatically manage EC2 instances, ensuring website availability, scalability, fault tolerance, and elasticity.
   
12. **GitHub for Version Control**: Web files were stored on GitHub for version control and collaboration.
   
13. **SSL Certificate**: Secured application communications using AWS Certificate Manager.
   
14. **SNS Notifications**: Configured Simple Notification Service (SNS) to alert about activities within the Auto Scaling Group.
   
15. **Route 53 for DNS**: Registered the domain name and set up a DNS record using Route 53.
   
16. **Elastic File System (EFS)**: Used EFS for a shared file system between the web servers.
   
17. **Relational Database Service (RDS)**: Used RDS for the WordPress database.

## Installation Script

Below is the script used to set up the WordPress website on the EC2 instances:

```bash
# Switch to root user
sudo su

# Update the software packages on the EC2 instance 
sudo yum update -y

# Create an HTML directory 
sudo mkdir -p /var/www/html

# Environment variable for EFS DNS Name
EFS_DNS_NAME=fs-0d34bf6a50dcce44e.efs.us-east-2.amazonaws.com

# Mount the EFS to the HTML directory 
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

# Install the Apache web server, enable it to start on boot, and then start the server immediately
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

# Install PHP 8 along with several necessary extensions for WordPress to run
sudo dnf install -y \
php \
php-cli \
php-cgi \
php-curl \
php-mbstring \
php-gd \
php-mysqlnd \
php-gettext \
php-json \
php-xml \
php-fpm \
php-intl \
php-zip \
php-bcmath \
php-ctype \
php-fileinfo \
php-openssl \
php-pdo \
php-tokenizer

# Install the MySQL version 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 

# Install the MySQL server
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm 
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server 

# Start and enable the MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html 

# Download WordPress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# Create the wp-config.php file
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Edit the wp-config.php file
sudo vi /var/www/html/wp-config.php

# Restart the web server
sudo service httpd restart
```

## Project Repository

The reference diagram, scripts, and additional documentation for this project can be found in the GitHub repository: https://github.com/simbaaws88/Host-a-WordPress-Website-on-AWS-

## Conclusion

This project showcases a comprehensive deployment of a WordPress website on AWS, ensuring high availability, scalability, security, and ease of management through various AWS services. The use of automation and best practices in cloud architecture ensures that the website is resilient and ready for production workloads.

---
