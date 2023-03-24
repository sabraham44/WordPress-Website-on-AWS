<p align="center">
<img src="https://user-images.githubusercontent.com/126641333/227334884-0c37ef0e-7af2-4320-ab2d-473521c02b4d.png"/>
</p>

<h1>WordPress Website using AWS </h1>
In this tutorial, we set up a wordpress website using AWS services. Our VPC will have a public and private subnets in two availiability zones (AZ). The two AZs will ensure high availiability (HA) and fault tolerance. We will have an Internet Gateway which will be used to allow communication between instances in the VPC and the internet. Resources such as Nat Gateway, Bastion host, and Application Load Balancer will be using public subnets, while webservers and database servers will use private subnets. </br>




<h2>Create a VPC</h2>

- Search for 'VPC' in the Search box, click on the first choice
- Under VPC settings, give a name to the VPC (we used Dev VPC)
- Select IPv4 CIDR manual input
- IPv4 CIDR - 10.0.0.0/16
- Leave the other settings default 
- Click on Create VPC

<img src="https://user-images.githubusercontent.com/126641333/227365275-c2774dda-fa03-4854-a2f3-30bbd1cc78ac.png"/>

- Once the VPC is created, on the right you click on 'Select a VPC' box and choose your VPC
- If you VPC is not on the list, please refresh the page
- this is also where you can see all the VPC you created

<img src="https://user-images.githubusercontent.com/126641333/227365335-c0c2dd8b-4b9e-4ca1-8937-f375598ca669.png"/>

<h2>Create Nat and Security Gateways</h2>
The Nat Gateway allows the instances in the private App subnets and private Data subnets to access the internet. The Private Route Table is associated with the private subnets and routes traffic to the internet through the Nat gateway.
<img src="https://user-images.githubusercontent.com/126641333/227541290-add3ccf6-d242-465a-a4bd-dcaeeab092a4.png"/>

<h2>Creating an Internet Gateway</h2>
<ul>
  <li>From VPC home page, on the left under 'Virtual Private Cloud'</li>
  <li>Select Internet Gatewayss</li>
  <li>Then click, Create internet gateway</li>
  <li>Give your Intenet gateway a name and click Create</li>
</ul>
<img src="https://user-images.githubusercontent.com/126641333/227371942-72ea2fd1-452c-41b6-bb28-dfbc06a5e9ce.png"/>
 <h2>Attaching Internet Gateway and VPC</h2>
 <ul>
  <li>Once you create your IG, you will see a Green banner on top of the page asking you to attach to a VPC, click on it'</li>
  <li>If you don't see the green banner, you can go to Actions and select Attach to VPC</li>
  <li>On the available VPC box, click on the search and your Dev VPC will be listed, select it and click Attach internet gateway</li>
</ul>
<img src="https://user-images.githubusercontent.com/126641333/227372926-2ab03381-e0a1-4425-a032-219e627a728d.png"/>

<h2>Creating Security Groups</h2>

1. ALB Security Group<br>
 Port = 80 and 443<br>  Source = 0.0.0.0/0

2. SSH Security Group<br>
   Port = 22<br> Source = your IP address

3. Webserver Security Group<br>
   Port = 80 and 443 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Source = ALB Security Group<br>
   Port = 20&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Source = SSH Security Group

4. Database Security Group</br>
   Port = 3306<br>   Source = Webserver Security Group

5. EFS Security Group</br>
   Port = 2049&nbsp;&nbsp;  Source = Webserver Security Group, EFS Security Group<br>
   Port = 22&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    Source = SSH Security Group


<h2>Create RDS instance</h2>
We will be using MySQL RDS database 
<img src="https://user-images.githubusercontent.com/126641333/227543319-f1ec6c36-3e3b-435e-b3d1-c286fbf5bbbe.png"/>


<h2> Create Elastic File System (EFS)</h2>
We will be using Amazon EFS so that the webservers can have access to shared files. The EFS Mount Targets are in each AZ in the VPC and the webservers will use the mount targets to connect to the EFS. 
<img src="https://user-images.githubusercontent.com/126641333/227568033-34baa070-5966-4332-a2cf-3379064b0045.png"/>


<h2>Create a Key Pair</h2>
<img src="https://user-images.githubusercontent.com/126641333/227569258-7a517cf1-0fe3-41b6-9bdd-484f855875b1.png"/>
<img src="https://user-images.githubusercontent.com/126641333/227569333-3980e0b2-2ab2-4d72-8bb9-a0862fb52619.png"/>

<h2>SSH Into EC2 Instance</h2>
We will be using Putty to SSH into our EC2.
<img src="https://user-images.githubusercontent.com/126641333/227571650-b2fc92cb-db0b-41ae-9ee0-cb594afdc05d.png"/>
<img src="https://user-images.githubusercontent.com/126641333/227570177-6bae0d89-f4d5-4231-a6e6-9cc08a5e32f7.png"/>

<h2>Create an Application Load Balancer (ALB)</h2>
We are using EC2 instance to host our website, ALB will be used to distribute web traffic across our EC2 in multiple AZs. 
<img src="https://user-images.githubusercontent.com/126641333/227571083-bd8feb47-7800-4e24-9807-2e0ba3d741a1.png"/>

<h2>Register a New Domain in Route 53</h2>
We are using Route 53 to register our Domain name and create a record set.

<img src="https://user-images.githubusercontent.com/126641333/227572740-8a461114-6bc3-476b-bd6c-1deb152405d9.png"/>
<img src="https://user-images.githubusercontent.com/126641333/227572809-fd89c964-4e4c-4c59-bdcc-69b9971753ad.png"/>


<h2>Launch Bastion Host</h2>
To SSH into the instances in the private subnet 
<ul>
  <li>OFirst, we will launch an EC2 instance in the public subnet</li>
  <li>Next we will SSH in the instance we launched in the public subnet</li>
  <li>Then from the instance in the public subnet, we can SSH into any instance in the 
     private subnets</li>
</ul>
<img src="https://user-images.githubusercontent.com/126641333/227577649-4311edf2-a8b0-459f-b718-862db9909027.png"/>
<img src="https://user-images.githubusercontent.com/126641333/227574373-d12e5c69-fe3c-43c5-9ff1-ffd009b307e9.png"/>
<img src="https://user-images.githubusercontent.com/126641333/227577454-9a93505f-e5f5-4bb9-b97d-4fc372f04020.png"/>
  
  
 <h2> Creating Auto Scaling Group</h2>
  Using Auto Scaling Group to dynamically create our EC2 instance to make our website highly available scalable, fault-tolerant, and elastic.
  <img src="https://user-images.githubusercontent.com/126641333/227577113-49dfbca3-a9fc-457c-ac15-d980104b8915.png"/>

<h2> Install WordPress Theme and Template</h2>
<img src="https://user-images.githubusercontent.com/126641333/227612807-f89274ef-b140-4ddc-aeed-51050bff9e92.png"/>




