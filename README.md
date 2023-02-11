# setup-jenkins-with-awsEC2


## Jenkins on AWS

Jenkins is an open-source automation server that integrates with a number of AWS Services, including AWS CodeCommit, AWS CodeDeploy, Amazon EC2 Spot, and Amazon EC2 Fleet. You can use Amazon Elastic Compute Cloud (Amazon EC2) to deploy a Jenkins application on AWS.

In this tutorial, you will go through the process of setup Jenkins in the EC2 instances. Firstly, you will lunch the EC2 instance and configure the security group inbound traffic to allow SSH and the port for Jenkins, then you will SSH into the EC2 instance and install Jenkins.

## Steps to follow : 

1. Prerequisites
2. Create a key pair using Amazon EC2 - **skip this if you have already done this before.**
3. Create the security group rules - **skip this if you have aleardy done this.**
4. Lunch EC2 instance. 
5. SSH into EC2 and install and configure Jenkins. 
6. Clean up. 


### Step 1 : Prerequisites

- You must have AWS account
- An amazon EC2 key pair. 
- AWS IAM User with programmatic key access and permisition to lunch EC2 instances. 


### Step 2 : Creating a key pair 
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

``` powershell 
$ chmod 400 <Key_pair_name>.pem 
```
### Step 3: Creating a security group 

A security group acts as a firewall that controls the traffic allowed to reach one or more EC2 instances. 
When you launch an instance, you can assign it one or more security groups.You add rules that control the traffic 
allowed to reach the instances in each security group. You can modify a security group’s rules any time, and the new rules
take effect immediately.

The ports need to be opend for this tutorial are: 
  - 443 for SSH /HTTPS to allow inbound traffic from your static IP or from anyware.
  - 80 to allow inbound traffic from anyware.

1. if you want to allow only access from your PC IP , you can check your IP from this link http://checkip.amazonaws.com/ 
    otherwise , use the IP address range 0.0.0.0/0 . 

2. Sign in to the AWS Management Console.
3. Open the Amazon EC2 console by selecting EC2 under Compute.


  <img src="img/img1.png" />

4. In the left-hand navigation bar, select Security Groups, and then select Create Security Group.

<img src="img/img2.png" />

5. In Security group name, enter WebSG or any preferred name of your choice, and provide a description.
6. Select your VPC from the list. You can use the default VPC.
7. On the Inbound tab, add the rules as below : 

<img src="img/img3.png" />


### Step 4 : Launching an Amazon EC2 instance

After your are done with SG and key piar configuration , you can now lunch EC2 instance. 
To do so , follow the steps below : 

1. Sign in to the the AWS Management Console.
2. Open the Amazon EC2 console by selecting EC2 under Compute.
3. From the Amazon EC2 dashboard, select Launch Instance.

<img src="img/img4.png" />

4. Then choose the Amazon Machine Image (AMI) based on your requirments , in this tutorial , we will use Amazon Linux AMI to stay on the free tier with type t2.micro.

<img src="img/img5.png" />

5. Scroll down and select the key pair you created in the creating a key pair section above or any existing key pair you intend to use:

- Select Select an existing security group.
- Select the WebSG security group that you created.
- Select Launch Instance.

<img src="img/img6.png" />

6. In the left-hand navigation bar, choose Instances to view the status of your instance. Initially, the status of your instance is pending. After the status changes to running, your instance is ready for use.

<img src="img/img7.png" />


### Step 5 : Installing and configuring Jenkins

After lunching the EC2 instance , Jenkins can be installed properly.

In this step you will deploy Jenkins on your EC2 instance by completing the following tasks:

  - Connecting to your Linux instance.
  - Downloading and installing Jenkins.
  - Configuring Jenkins.

###  Connecting to your Linux instance:

There are four ways to connect to your EC2 ( EC2 Instance Connect , Session Manager , SSH Client and EC2 serial console ). 
In this tutorial we will use EC2 Instance Connect : 

<img src="img/img8.png" />

After connect : 

<img src="img/img9.png" />

**Note** : If you want to connect using SSH , use the terminal if you are on MacOS , or PuTTY if you are on Windows. 

### Downloading and installing Jenkins: 

Completing the previous steps enables you to download and install Jenkins on AWS. To download and install Jenkins:



``` powershell
#Ensure that your software packages are up to date on your instance

[ec2-user ~] $ sudo yum update –y
```

``` powershell
#Add the Jenkins repo using the following command:

[ec2-user ~]$ sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

``` powershell
#Import a key file from Jenkins-CI to enable installation from the package:

[ec2-user ~]$ sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

[ec2-user ~]$ sudo yum upgrade
```
``` powershell
#Install Java:

[ec2-user ~]$ sudo amazon-linux-extras install java-openjdk11 -y
``` 
``` powershell
#Install Jenkins:

[ec2-user ~]$ sudo yum install jenkins -y

#Enable the Jenkins service to start at boot:

[ec2-user ~]$ sudo systemctl enable jenkins

#Start Jenkins as a service:

[ec2-user ~]$ sudo systemctl start jenkins

```
``` powershell
#Check the status of the Jenkins service

[ec2-user ~]$ sudo systemctl status jenkins

```

![Alt text](img/Screenshot%202023-02-11%20at%205.32.32%20PM.png)


## Configuring Jenkins

Next step is to run Jenkins web page and configure it , to do so , please follow the steps below : 

1. Connect to http://<your_server_public_DNS>:8080 from your browser. You will be able to access Jenkins through its management interface:


![Alt text](img/Screenshot%202023-02-11%20at%206.07.47%20PM.png)

2. To nter the password , in the CLI navigate this path and copy the password : /var/lib/jenkins/secrets/initialAdminPassword 

``` powershell
[ec2-user ~]$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![Alt text](img/Screenshot%202023-02-11%20at%206.12.12%20PM.png)

3. The Jenkins installation script directs you to the Customize Jenkins page. Click Install suggested plugins.

![Alt text](img/Screenshot%202023-02-11%20at%206.14.35%20PM.png)

4. Once the installation is complete, the Create First Admin User will open. Enter your information, and then select Save and Continue.

![Alt text](img/Screenshot%202023-02-11%20at%206.17.46%20PM.png)


![Alt text](img/Screenshot%202023-02-11%20at%206.18.04%20PM.png)

5. On the left-hand side, select Manage Jenkins, and then select Manage Plugins.

6. Select the Available tab, and then enter Amazon EC2 plugin at the top right.

7. Select the checkbox next to Amazon EC2 plugin, and then select Install without restart.

![Alt text](img/Screenshot%202023-02-11%20at%206.21.21%20PM.png)

8. Once the installation is done, select Back to Dashboard.

9. Select Configure a cloud if there are no existing nodes or clouds.

![Alt text](img/Screenshot%202023-02-11%20at%206.23.09%20PM.png)

10. If you already have other nodes or clouds set up, select Manage Jenkins.

![Alt text](img/Screenshot%202023-02-11%20at%206.25.16%20PM.png)

- After navigating to Manage Jenkins, select Configure Nodes and Clouds from the left hand side of the page.

![Alt text](img/Screenshot%202023-02-11%20at%206.28.44%20PM.png)

- From here, select Configure Clouds.

![Alt text](img/Screenshot%202023-02-11%20at%206.29.53%20PM.png)

11. Select Add a new cloud, and select Amazon EC2. A collection of new fields appears.

![Alt text](img/Screenshot%202023-02-11%20at%206.31.43%20PM.png)

12. Click Add under Amazon EC2 Credentials

![Alt text](img/Screenshot%202023-02-11%20at%206.33.14%20PM.png)

- From the Jenkins Credentials Provider, select AWS Credentials as the Kind.

![Alt text](img/Screenshot%202023-02-11%20at%206.37.01%20PM.png)

- Scroll down and enter in the IAM User programmatic access keys with permissions to launch EC2 instances and select Add.

![Alt text](img/Screenshot%202023-02-11%20at%206.38.30%20PM.png)

- Scroll down to select your region using the drop-down, and select Add for the EC2 Key Pair’s Private Key.

![Alt text](img/Screenshot%202023-02-11%20at%206.45.33%20PM.png)

- From the Jenkins Credentials Provider, select SSH Username with private key as the Kind and set the Username to **ec2-user** 

![Alt text](img/Screenshot%202023-02-11%20at%206.49.21%20PM.png)

- Scroll down and select Enter Directly under Private Key, then select Add.

![Alt text](img/Screenshot%202023-02-11%20at%206.51.17%20PM.png)

- Open the private key pair you created in step 2 and paste in the contents from "-----BEGIN RSA PRIVATE KEY-----" to "-----END RSA PRIVATE KEY-----". Select Add when completed.

![Alt text](img/Screenshot%202023-02-11%20at%206.58.43%20PM.png)

- Scroll down to "Test Connection" and ensure it states "Success". Select Save when done

![Alt text](img/Screenshot%202023-02-11%20at%207.00.16%20PM.png)


**You are now ready to use EC2 instances as Jenkins agents.**

## Cleaning up

After completing this tutorial, be sure to delete and terminate the AWS resources that you created so you do not continue to accrue charges.

# END OF STEPS

