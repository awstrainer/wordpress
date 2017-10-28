# wordpress
	1. Security GroupCreate two security Group web-DMZ-SG(Port 80, 22 Source - 0.0.0.0/0) 
	RDS-SG(mysql-arora-3306, Source - web-DMG-SG)
	
	2. RDSMulti-AZ - NoAllocated Space - 5GBVPC - Default VPCPublicly Accessibly - NoVPC Security Group - RDS-SG
	3. S3 Accesss3-admin-full-access
	4. EC2VPC - DefaultAuto-Assign Public IP - EnabledIAM - Role - s3-admin-full-accessBoot Strap Script
	#!/bin/bash
	yum update -y
	yum install httpd php php-mysql stress -y
	cd /etc/httpd/conf
	cd /var/www/html
	echo "healthy" > healthy.html
	wget https://wordpress.org/latest.tar.gz
	tar -xzf latest.tar.gz
	cp -r wordpress/* /var/www/html/
	rm -rf wordpress
	rm -rf latest.tar.gz
	chmod -R 755 wp-content
	chown -R apache:apache wp-content
	service httpd start
	chkconfig httpd on
	
	--> we will put the config file in S3, so its easy incase someone changes the password of database or anything changes, so don’t have to create fresh AMI, and also it saves cost.Security Group - Web-DMZ-SG
	5. Take down RDS Endpoint.Access wordpress site and configure sudo su -cat >/var/www/html/wp-config.phpcat > copy the content into file /var/www/html/wp-config.php Configure the rest of the setup for wordpress
	6. aws s3 mb s3://wordpressnsnaws s3 cp wp-config.php s3://wordpressnsn
	
	7. ELBNetwork My- ELBall AZ, Name: CustomNewTargetGrp-WPhealth threshold - 2Interval - 10 secondsAdd Running Instance. Load Balancers > Target Groups  > Look for Instances Status > it should be healthy from initial status.Wait for sometimeChange the wordpress url for ELB
	8. Create Launch ConfigurationAdvanced conf
	#!/bin/bash
	yum update -y
	yum install httpd php php-mysql stress -y
	cd /var/www/html
	echo "healthy" > healthy.html
	wget https://wordpress.org/latest.tar.gz
	tar -xzf latest.tar.gz
	cp -r wordpress/* /var/www/html/
	rm -rf wordpress
	rm -rf latest.tar.gz
	chmod -R 755 wp-content
	cd /var/www/html
	aws s3 cp s3://wordpressnsn/wp-config.php  wp-config.php 
	chown -R apache:apache wp-content
	service httpd start
chkconfig httpd on
