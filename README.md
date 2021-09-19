
# Infrastructure automation with Terraform on AWS for CI/CD pipeline to host a java web application.

# Abstract  
To construct a CI/CD pipeline to host a java web application.  Assume you are developing a web application; you have 15 developers working in building the project with a shard Git repository and you want to deploy the web app on 100 web servers in real time while the developers are committing a code. In such case a Continuous Integration (CI) builds and tests the web app after developers made a code change on git whereas a continuous delivery pushes approved changes to 100 web servers. CI/CD allow us to deliver code changes more frequently using automation.  
# Objective  
The aim of the project is to get hands on experience in CI/CD pipeline and on cloud. By the end of the project, we will have a single terraform repository where you can type terraform apply command and it will do CI/CD pipeline for you.  

## Pre-requisite
- AWS account 
- Amazon Machine Image (AMI)  Red Hat Enterprise Linux 8.x
- Terraform required_version = ">= 0.12"

## Get start 

1. [Install terraform ](https://learn.hashicorp.com/tutorials/terraform/install-cli)
2. Git clone 
3. [create aws EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)

    - jenkins-key.pem
    - ansible-key.pem
    - tomcat-key.pem
    
4 Provide your private key under Provisioner Connection Settings

  - Provisioner Connection Settings directory of a file  
  
         CI-CD-PipelineOnAws/modules/ansible/instance.tf
         CI-CD-PipelineOnAws/modules/jenkins/instance.tf
         CI-CD-PipelineOnAws/modules/tomcat/instance.tf
        
        connection {
              type        = "ssh"
              host        = self.public_ip 
              user        = "ec2-user" 
              private_key = file("~/key/ansible-key.pem") 
             } 
      
5. Specify corrrect PATH for file provisioning to excute bash scripte

    - file provisioning  directory of a file
    
          CI-CD-PipelineOnAws/modules/ansible/instance.tf
          CI-CD-PipelineOnAws/modules/jenkins/instance.tf    
          CI-CD-PipelineOnAws/modules/tomcat/instance.tf
       
          provisioner "file" {
	        	source     = "~/bash-script/ansible.sh" 
		      destination  = "/tmp/ansible.sh" 
	            }  
      
      
6.  ```sh
    cd CI-CD-PipelineOnAws/dev
      ```

7. Export AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY

     ```sh
         $ export AWS_ACCESS_KEY_ID="anaccesskey"
         $ export AWS_SECRET_ACCESS_KEY="asecretkey"
      ```
 
8. terraform apply 

# Post-Install Configuration 
 ## Jenkins 
 
 	1 Browse http:your-target-ip:8080
 	2 Username admin
	3 Password Location:/var/lib/jenkins/secrets/initialAdminPassword
	4 Change password 
	5 Configure java path
	6 Create maven project
	7 Configure maven path
	8 Provide your project git url
 ## Tomcat 
	
	1 Browse http:your-target-ip:8080
	2 Allow tomcat to login from browser type  #find -name context.xml# 3 context.xml files.
	  comment () Value ClassName field on files which are under webap directory. 
	3 Restart tomcat -  type tomcatup 
	4 Copy the following file under /opt/apache-tomcat-8.5.35/conf/tomcat-user.xml 
	
        	<role rolename="manager-gui"/> 
		<role rolename="manager-script"/> 
		<role rolename="manager-jmx"/> 
		<role rolename="manager-status"/> 
		<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/> 
		<user username="deployer" password="deployer" roles="manager-script"/> 
		<user username="tomcat" password="root123" roles="manager-gui"/> 	
     	 
	5 Restart service and try to login to a tomcat application from the browser use tomcat user with password root123.  
	
## Ansible 
	
	1 su - ansadmin  
	2 ssh-keygen
	3 ssh-copy-id target-ip-address (tomcat-ipaddress)
	4 nano /etc/ansible/hosts  add tomcat ip address
	5 test using ansible all -m ping
	6 write playbooks under /opt/playbooks
# cicd-newproject
