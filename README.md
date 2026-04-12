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