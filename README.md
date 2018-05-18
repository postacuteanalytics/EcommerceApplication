Prerequisites:
  1.	We need one Git Hub account where developer can push code to private repository.
  2.	One Docker-Hub account for push and pull the docker images to docker-hub.
  3.	AWS Account required for launching instances and creating Kubernetes cluster.

Software Tool Stack
  1.	Docker: We are using docker for containerized the application to automate infrastructure and resource utilization
  2.	Jenkins: For continuous integration of every tools
  3.	SonarQube: For testing the quality of code
  4.	Cloud Formation / Ansible: Orchestration tool for Dev and Prod environment

Assumptions
  1.	Required one region (US-East-1) for creating environments
  2.	Need 2 VPC (1 VPC required for Dev and Prod Environment each, 1 VPC for Jenkins and Database)
  3.	2 AWS EC2 instance required for following:
      1.	Sandbox (Ubuntu 14.04-t2. Large with Jenkins and required plugins) => 1
      2.	Database (Ubuntu 16.04-t2. micro with disabled delete on termination) => 1
      3.	Dev Env => (Ubuntu 14.04-t2. micro)
      4.	Prod Env => (Ubuntu 14.04-t2. micro)
  4.	For CI we can use Jenkin Server and for provisioning we need AWS CloudFormation  
  5.	Using Elastic IP in Database Server so if it reboots public IP will remain same.
  6.	In Jenkins we need following plugins to install
      1.	SonarQube Scanner for Jenkins
      2.	Docker – plugin
      3.	Jenkins-CloudFormation –plugin
      4.	Publish Over SSH
      5.	SSH plugin

Deployment Steps:
  1.	Sandbox (Jenkins with Docker, SonarQube Scanner for Jenkins, Jenkins-CloudFormation-plugin, Publish Over SSH & SSH plugin) instance has been created in AWS Availability Zone -A
  2.	Database Server has been installed in AWS EC2 t2-micro Availability Zone A
  3.	Development team will develop the 3-tiered wed application with static assets (i.e. html, CSS, jQuery) using AJAX.
  4.	After developing the code, developer will push the code in git-hub and then Jenkin will automate the first jobs.
  5.	In Jenkin initially, code quality (SonarQube) will be checked along with unit test (PHPunit), and if the first job completes successfully then only Development Env (CloudFormation) will be create with the help of orchestration tool manually by running URL of second job.
  6.	Now CloudFormation will automate the creation Dev Environment in AWS.
  7.	Database instance will have existing database required by application with tables and exposed on port 5432.
  8.	Application will be dockerize in web container with the help of docker image and expose on port 8080.
  9.	After Deployment Development Team will perform crosscheck the functionality of other modules and if development team find any functionality missing then they will again rebuild the code and push to GitHub
  10.	If all issues are closed, then QA team will test application in Dev environment and if they find no issues then Production Env begin to create in AWS manually by running URL of third job.
  11.	Application Container will be packaged with Apache2, php5 dependencies required by application.

Alternate Solution Recommendation
  1)	Need apache2 web server to run shopping cart application which is coded in static content using php and ajax.
  2)	For Determine the size of resource we should know how much memory need by application and on which platform it has been written and tools required to run.
  3)	Need EC2 instances size between t2. micro to t2. large in resources , tools and techniques use for provisioning are Jenkin , Docker , Kubernetes , Git , SonarQube , Jira , Unit Testing , Ansible or Cloud Formation  and AWS services with CI CD.  
  4)	We can verify the resources are working in healthy condition with cloud watch.
  5)	Through Kubernetes console also we can check the health of cluster which deployed in AWS
  6)	We can use load balancer DNS in browser for checking application is running and interacting with webservice.
  7)	If Application is able to return the database value, then it is talking with database.
  8)	Whenever developer push new code to GitHub, Jenkin will automate that code for quality check then create the developer environment.
  9)	For Production Environment, Jenkins will automate the orchestration to create Kubernetes cluster with one master and two minions. In both minions pods will create and in that pods we deploy the application image from docker hub. Handling the minions from the master only.
  10)	If any bad deployment occurs, we can run the Jenkin job again for redeployment.
  11)	 For monitoring and alerting we can use any one of them tool Grafana, Prometheus, ELK Stack, Site 24/7 or AWS CloudWatch.
  12)	We can use dockbeat for transfer the logs for monitoring docker container to ELK stack for monitoring container.
  13)	We can scale the application pods vertically through vertical pods autoscaling
  14)	Scaling Kubernetes minions or pods horizontally by enable it in kube-up.sh file
  15)	By monitoring the server component like CPU usage or disk usage through ELK or Cloud Watch we can determine that resources needed to be scale or not.
  
Diagrammatic DevOps Flow Of Shopping Cart
  
  ➔	First Architecture
    ![screenshot from 2018-05-17 21_07_42](https://user-images.githubusercontent.com/39376140/40201222-75b96cde-59ec-11e8-9158-dd0bd06d83e6.png)
 
  ➔	Second Architecture
    ![image](https://user-images.githubusercontent.com/39376140/40201473-275cd6d8-59ed-11e8-86f5-4d48d4f964fc.png)
    
    
 1.	go about creating the desired resource in Kubernetes cluster within AWS?
 
    a)	How would you go about determine the size( i.e. # of cpu cores, memory, etc) of the resource
    
       Solution: By Analyzing the application (Size of application, How much logs are generated per day/month), what is the traffic of                   site (how many number of users are hitting the website)
      
    b)	Which tool and technique would you use for provision
    
       Solution: CloudFormation, Terraform, Ansible
      
 2.	verify the individual resource are working
 
    Solution: with the help of Monitoring (CloudWatch, ELK stack....)
 3.	verify the static assets can talk to web services
 
    Solution: By passing the request through browser/using test cases
 4.	verify the static assets can talk to web services
 
    Solution: By passing the request through browser login Page /using test cases
 5.	get the new code and database object changed into the development environment?
 
    a)	Which tool and technique would you use for CI and/orCD.
    
       Solution: Jenkins, Ansible
 6.	get the new code and database object changed into the Production environment?
    a)	Which tool and technique would you use for CI and/or CD.
    
       Solution: Jenkins, Ansible
    b)	how would you rollback a bad deployment
    
       Solution: in Kubernetes deployment file with the modification of Image version rollback is happening

Bonus Points: 
  1. How would you implement monitoring and alerting of an application? 
     
     a. Which tool and technique would you use for monitoring 
    
        Solution: Prometheus, ELK stack, Grafana,
    
     b.	Which tool and technique would you use for alerting
    
        Solution: Prometheus, ELK stack, Grafana,
  2.	What security concern would you raise if any 
  
        Solution:
          Instead of exposing all ports in the instance level, open required ports source as with in the subnet/vpc, with specific ip             range. For the services in Kubernetes expose the ports with different number
  3.	How would scale the application horizontally
  
        Solution: with the help of AWS auto scaling the application horizontally is done
  4.	How would you determine the if resource need to be scale
  
        Solution: While monitoring the resource with threshold levels (CPU utilization, Disk Usage) estimate the resource need scaling                     or not

    
    
