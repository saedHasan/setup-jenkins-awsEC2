# setup-jenkins-with-awsEC2


Jenkins on AWS

Jenkins is an open-source automation server that integrates with a number of AWS Services, including: AWS CodeCommit, AWS CodeDeploy, Amazon EC2 Spot, and Amazon EC2 Fleet. You can use Amazon Elastic Compute Cloud (Amazon EC2) to deploy a Jenkins application on AWS.

In this tutorial , you will go through the process of setup Jenkins in EC2 instance. Firstly , you will lunch EC2 instance and confgire the security group inboud trafic to allow SSH and the port for Jenkins , then you will SSH into EC2 instance and install Jenkins. 

Steps to follow : 

1. Prerequisites
2. Create a key pair using Amazon EC2 - skip this if you have already done this before. 
3. Create the security group rules - skip this if you have aleardy done this. 
4. Lunch EC2 instance. 
5. SSH into EC2 and install and configure Jenkins. 
6. Clean up. 


Step 1 : Prerequisites

a. You must have AWS account
b. An amazon EC2 key pair. 
c. AWS IAM User with programmatic key access and permisition to lunch EC2 instances. 


Step 2 : Creating a key pair 
A key pair is a combination of a public key that is used to encrypt data and a private key that is used to decrypt data. 

To create your key pair: 

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/ and sign in.
2. In the navigation pane, under NETWORK & SECURITY, select Key Pairs.
3. Select Create key pair.
4. For Name, enter a descriptive name for the key pair.
5. For File format, select the format in which to save the private key.
     - For OpenSSH compatibility, select pem.
     - For PuTTY compatibility, select ppk.
6. Select Create key paira and then the private key file downloads automatically.
7. If you want to access the instance using SSH in macOS or lINUX enviroment , please run below command to set the permisition for the private
key .pem file 


  $ chmod 400 <Key_pair_name>.pem 

Step 3: Creating a security group 

A security group acts as a firewall that controls the traffic allowed to reach one or more EC2 instances. 
When you launch an instance, you can assign it one or more security groups.You add rules that control the traffic 
allowed to reach the instances in each security group. You can modify a security group’s rules any time, and the new rules
take effect immediately.

The ports need to be opend for this tutorial are: 
  1. 443 for SSH /HTTPS to allow inbound traffic from your static IP or from anyware.
  2. 80 to allow inbound traffic from anyware.

1. if you want to allow only access from your PC IP , you can check your IP from this link :http://checkip.amazonaws.com/ 
    otherwise , use the IP address range 0.0.0.0/0 . 

2. Sign in to the AWS Management Console.
3. Open the Amazon EC2 console by selecting EC2 under Compute.


<img src="img/img1.png" />

4. In the left-hand navigation bar, select Security Groups, and then select Create Security Group.
<img src="img/img2.png" />


