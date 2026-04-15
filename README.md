# aws-cloud-vpc-lab

Cloud networking is an important skill to have since many companies are shifting from on prem to to the cloud and knowing cloud architecture and networking helps with that.

# VPC

The first step is to to the VPC page in AWS and when there click on the create VPC button, then see this:   
![VPC](./images/vpc.png)    
Make sure to have the option as VPC only, then name it something like "Cloud-VPC-Lab". Then for the IPv4 CIDR put 10.0.0.0/16, turn on IPv4 CIDR, keep tenancy to default and then click on create. After a second, the VPC should be up.   
Then click on the actions tab and click on Edit VPC settings and see this:  
![Dns](./images/dns-settings.png)   
Then click on enable DNS hostnames and click on save.

# Subnets

After saving, go the left side of the page and click on subnets page and then click on Create a Subnet and see this:    
![Subnet new](./images/subnet.png)  
For VPC ID choose the one where the lab was created and then go create the settings for the public one. 
Name the first subnet "Public-Subnet-1A", then for availablity zone pick either no preferance or us-east-1a and for the IPv4 Subnet CIDR block name it 10.0.1.0/24, then click on add to a subnet.   
For the second subnet, name it "Private-Subnet-1A", then for avaiablity zone choose us-east-1b, then for IPv4 subnet choose 10.0.2.0/24, then click on to add a subnet.
For the third subnet, name it "Private Subnet-1B", availablity zone us-east-1b, then for IPv4 subnet CIDR pick 10.0.3.0/24. Then click on create subnet.    
Then you should see the subnets created.    
Then select the public subnet, click on actions and select the edit subnet settings and see this:   
![Subnet edit](./images/edit.png)   
Then click on the option to enable auto-assign public IPv4 addresses, then click on save.

# Internet Gateway

Next is to establish a internet gateway, click on internet gateways in the left side of the screen, then click on create a internet gateway and see this:   
![Gate](./images/gate.png)  
Name it Cloud-VPC-Lab-IGW, then click on create internet gateway. After creation, click on the attach to VPC button which will then allow you to choose which VPC you want to attach to. In this case, it will be the VPC lab one. Then click on attach internet gateway.   

# NAT Instance

Then for the NAT Instance go to EC2 and click on launch instance which will take you to the EC2 creation screen. Name it NAT-Instance. Then in AMI, click on browse more AMIs and go to the community AMIs tab and search for amzn-ami-vpc-nat and then click on select for the first option.   
For the instance type select the t2.micro, then click on create key pair and name it cloud-lab with the type set to rsa and .pem and click on create.   
Then for the network settings click on edit, then change the VPC to the Cloud-Lab-VPC, keep the public-subnet-1a as the subnet and keep auto-enable, then select on the option to create a new security group.  
Name the new security group NAT-Instance-5G, have the description be Allow outbound from private subnets. Then for inbound rules for the type be all traffic, set the source to custom for source be 10.0.10.0/24 like so:  
![Group](./images/group.png)    
Then click on create a second security group rule, same settings as before with all traffic and custom but the source CIDR be 10.0.11.0/24. Then click on Launch Instance.  
After creation go to the instance pane and then select on the NAT Instance, then click on the actions tab -> networking and then click on the Change source/destination check:  
![Change](./images/change.png)  
Then click on it and see this:  
![Check](./images/check.png)    
Then make sure the stop button is selected and click on save.   
Afterwards you should see this: 
![Check2](./images/change2.png)

# Route Tables
For route tables go back to the VPC tab and then go click on the create route table button. 
For the first one name it Public-RT with the VPC for the lab selected like so:  
![Route1](./images/route1.png)  
Then click on create route table. After creation for the routes, go to the edit routes in the table pane and click on edit routes to see this:  
![Route Edit](./images/route-edit.png)  
Then click on the add route button, for destination select 0.0.0.0/0, for target select the internet gateway with the cloud-vpc-lab created, then click on save changes.    
After saving go to the subnet associations table and see this:  
![Subnet2](./images/subnet2.png)    
Then click on the edit subnet associations button, then in the edit associations options, select the public subnet and the private-subnet-1A and click on save associations. This will be public.   
For the Private, go back to to the route tables pane and select the create route table button. After creating go the edit routes like for the public one and in the same screen click on the add route button.  
For the destination, choose 0.0.0.0/24 and then for target select NAT-Instance, then click on save changes. 
Then for Private-RT go to subnet associations and click on edit subnet associations. Then select Private-Subnet-1A and 1B and hit save associations.    

# Security Groups
To reach security groups go to the left pane and click on security groups, then go select the orange button that says create security group and see this after clicking:    
![Group](./images/security.png) 
Name this first group Bastion-SG, description of SSH access from my IP only, VPC for Cloud-VPC-Lab, then for inbound rules click on add rule and change the type to SSH and source to My IP which should auto detect and leave outbound as default, then click on create security group.    
Then click on create security group again for the second group and name it Web-SG, description of HTTP/HTTPS from internet, SSH from Bastion with the same VPC as before, then for inbound rules have the first one be HTTP with a source anywhere IPv4, then a second rule with type HTTPS with source anywhere IPv4, then a third rule with type SSH and a custom source with it as Bastion-SG, then click on create security group.  
Then for the third group, click on create security group and name it App-SG with a description of Custom app port from Web tier only, same VPC as before, with the inbound rules for the first one being type custom TCP with port 8080 and source being custom for Web-SG, then add a second rule that is SSH with a custom for Bastion-SG, then click on create security group. 

# Test Instances and Validation
Then go the EC2 and create a new instance called Bastion-Host with Amazon Linux 2023 AMI, t2.micro, for key pair pick cloud-lab, then press edit on network settings. For VPC choose the Cloud-VPC-Lab with the Public-1A Subnet with auto-assigned Public IP enabled, then choose existing security group of Bastion-SG and then click on create/launch instance.  
Then for the web server one click on Launch Instance and name it Web Server with the Amazon Linux AMI with t2.micro and same key as before, then it network settings choose the VPC as before with Public-Subnet-1A, auto assinged public IP and Web-SG security group and then clcik create instance.  
Then the third one, the app server, click on launch instance and name it App-Server with the Amazon Linux AMI, t2.micro, same key as before, edit the network settings to have same VPC as before with it on hte Private-Subnet-1A, disable auto assign public IP and App-SG Security group.    
![Instance](./images/instance.png)  

Then in PowerShell of the Command line type ssh -i "C:\Users\username\Downloads\cloud-lab.pem" ec2-user@ip-address and should see this as a ssh test:   
![SSH](./images/ssh.png)    
For the next step is to SSH from the Bastion to a Private Instance, to do this type ssh -A -i "C:\Users\username\Downloads\cloud-lab.pem" ec2-user@54.174.223.21 for agent forwarding which should see like this:   
![2](./images/instance2.png)    
*You might also have to go and edit the inbound rules for App or Bastion and change it to 0.0.0.0/0.*
Then on the host type scp -i "C:\Users\user\Downloads\cloud-lab.pem" "C:\Users\user\Downloads\cloud-lab.pem" ec2-user@54.174.223.21:~ which should copy over to the bastion. Then on the bastion type chmod 400 cloud-lab.pem and then ssh -i cloud-lab.pem ec2-user@10.0.2.44 so I don't think agent forwarding will work here for right now.    
Then for a final test type ssh -i "C:\Users\user\Downloads\cloud-lab.pem" ec2-user@10.0.2.44 with the expected result:  
![Result](./images/result.png)  

# VPC Flow Logs
Next step is to go to Your VPCs, then select the Cloud-Lab-VPC, then select the actions dropdown and select the Create flow log option: 
![Flow log](./images/flow.png)  
Then select the filter for all and for the destination have it be a S3 Bucket. First go to the S3 page and click on Create Bucket and name the bucket cloud-lab-vpc-initials and then create the bucket.    
Then copy the ARN for the bucket in properties and go back to the flow page and copy the ARN into the S3 bucket ARN and for log record keep it as AWS default, then click on create flow log.   


# Linux Web Server
