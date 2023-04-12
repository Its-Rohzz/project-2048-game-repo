### - Created By:- Rohit Patange
# Jenkins CI/CD project
## Host-website on Docker container using Git-hub repo source-code with automation of Jenkins CI/CD Pipeline
### NOTE:- Before you do this project you have to atleast basic knowledge about Docker, Ansible and Jenkins tools for better understanding of this project👍...				                                                                                 
### 👻👻👻 WE WILL DO THIS PROJECT IN 5 STEPS 👻👻👻			
**👨‍💻Brif introduction of our project like whatever things we do in this project👨‍💻**

- Lets see we have one devloper and they write some code of game and we want to set-up that game on docker-containers with full automation, so for that we create one CI/CD pipeline ...
- here devloper commit the changes on git-hub...
- then jenkin trigger automatically through web-hook wit github repo then that will be pull the data and save it in workspace...after that jenkin can tranfer that data in ansible server using rsync command with passwordless autentication...

- then in ansible server we can install docker and create one dockerfile for crating image and we push that image on docker-hub for the back-up purpose and with that image create one container...
- and also we create one playbook and in that playbook we mention command of docker container running and we write couple of commands for remove our remaining containers and images whatever is created with our CI/CD pipeline...cause whatever times your jenkins will be triggerd so they create docker-image and docker-containers so we have to delete that old containers and images so for that we write some docker-remove commands and for that we use **Jenkins variables** ...

### we will use some devops tools...
###### -Required Things 
1) Git-GitHub 
2) Jenkins
3) AWS Cloud (Ec2-instances)
4) Docker & Docker-hub account
5) NGINX (HTTPD service)
6) Ansible


#### First we need 4 servers (ec2-instances) 

### Launch 4 instances with t2.medium instance-types and create security group with "alltcp protocol with anywhere and ssh also" [ Use ubuntu (free tier) 2nd image in AWS-Cloud...cause in ubuntu latest version you face some issue with Dockerfile ]
1) Devloper Server
2) Jenkins server
3) Ansible Server
4) Docker server (This is node for ansible server )

### 👻👻👻👻 HERE YOUR 1ST STEP IS STARTED...GIVE YOUR BEST BUDDY 👻👻👻👻


#### 👨‍💻Login in 1st server Jenkins (root user) 

###### 💻install java that is required to run your jenkins cause jenkins is written in java language and its supports 11 version of java only
      apt install openjdk-11-jre -y

###### install Jenkins from there official website and follow the commands for install jenkins for linux...
__👉👉👉      https://pkg.jenkins.io/debian-stable/__

###### 💻install also git for jenkins can pull your image from git-hub
      apt install git -y


#### 👨‍💻Login in 2nd Ansible server (root user) 
###### 💻Install Ansible  (after install if ansible is inactive then activate it using start command instead of status)
      yum update -y
      yum install ansible -y
      service ansible status   

###### 💻Then go to ansible host path and create one group " [ansible-nodes] " then add your docker-server private-IP in that group
      vim etc/ansible/hosts                                                            
	   
###### 💻Install docker also for create docker-image from our Dockerfile
      apt install docker.io -y
      service docker start
      service docker enable  
      service docker status    

###### 💻Ensure that you have to login your docker-hub account in this ansible server for pushing the docker-images
      docker login

**After that create ansible playbook and run this playbook for your docker servers in this playbook we have to mention some docker commands for run the container from our created docker-images and delete remaining containers and images...**

###### 💻Create "project-1.yml" file on "/home/ubuntu/" this path and write this script in your file and in this script you have to provide your docker-hub-id 👇👇👇
   
      - hosts: all
        connection: ssh
        become: yes
        tasks: 
	   - name: stop container 
	     shell: docker container kill container-1
	   - name: remove container
	     shell: docker rm container-1
	   - name: remove docker image
	     shell: docker rmi <type-docker-hub-user-id>/cicd-project-1
	   - name: create docker-container
	     shell: docker run -td --name container-1 -p 80:80 <type-docker-hub-user-id>/cicd-project-1  
	  
	   
###### 💻Then create file on " /home/ubuntu/ " this path of this name " Dockerfile " for crating container... write this script in your file and save it👇👇👇
   
            FROM nginx:latest
            COPY ./docker/* /usr/share/nginx/html/
            EXPOSE 80


#### 👨‍💻Login in 3rd  Docker-server (root user) 
###### install Docker
      apt install docker.io -y
      service docker start
      service docker enable  
      service docker status  


#### 👨‍💻Login in 4th Devloper server (root user) 
###### install git for devloper like they will change the code and commit that code and push into git-hub
      apt install git -y


### 👻👻👻👻 HERE YOU DONE YOUR 1ST STEP...LETS MOVE TO 2ND STEP 👻👻👻👻

**Do passwordless authentication in docker server and Ansible server for give access of docker server to our ansible server for run the commands in Docker server**
#### 👨‍💻Go to the Docker server (root user)
###### 💻go to this path then open sshd_config file using vim and give yes permission to in "password authentication" then uncomment the "permitrootlogin" & remove that "prhibit password" and type here "yes" in front of permitrootlogin and then save it
      vim /etc/ssh/sshd_config  
###### 💻restart sshd service  
      service sshd restart 
###### 💻set password to root user      
      passwd root
Type new password then re-type new password then hit enter
your password will be sucessfully set to your server 

#### 👨‍💻Go to Ansible server (root user)
###### 💻Create one public-key     
       ssh-keygen    
###### 💻Copy the key of docker-server into your ansible server       
       ssh-copy-id root@<private-ip of docker-server>  




### 👍Do the same process with jenkins server and ansible server
#### 👨‍💻go to ansible server (root server)
###### 💻go to this path then open sshd_config file using vim and give yes permission to in "password authentication" then uncomment the "permitrootlogin" & remove that "prhibit password" and type here "yes" in front of permitrootlogin and then save it
      vim /etc/ssh/sshd_config  
###### 💻restart sshd service  
      service sshd restart 
###### 💻set password to root user      
      passwd root
Type new password then re-type new password then hit enter
your password will be sucessfully set to your server 



#### 👨‍💻Go to jenkins server (root user)
###### 💻Create one public-key     
       ssh-keygen    
###### 💻Copy the key of ansible-server into your jenkins server       
       ssh-copy-id root@<private-ip of ansible-server>

###### 💻Also set the password for jenkins server root user
       passwd root   
type new password then re-type new password then hit enter
your password will be sucessfully set to your server 

###### 💻go to this path then open sshd_config file using vim and give yes permission to in "password authentication" then uncomment the "permitrootlogin" & remove that "prhibit password" and type here "yes" in front of permitrootlogin and then save it
       vim /etc/ssh/sshd_config


### 👻👻👻👻 HERE YOU DONE WITH YOUR 2ND STEP...LETS MOVE TO 3RD STEP 👻👻👻👻

#### 👨‍💻Login in your git-hub account and create one public repository 
**In your git-hub account we need to integrate our jenkins with our git-hub repo for if our devloper will be commit any changes in our git-hub soo our jenkins will be automatically triggered to run our jenkins created pipeline so for doing that follow this stepes 👇👇👇👇👇👇**

- go to your git-hub account and go to your source-code repo in source-code repo go to **( settings )** then click on **( webhooks )** then click on add webhook then in **payload url** type your **( jenkins url )** ( jenkins url means type your http://<your-jenkin-server-public-ip>:8080/github-webhook/ ) ...then in **( content type )** option select **application json** option...

- then in **( secret )** option you have to provide your jenkins secret token so for that go to your jenkins dashboard and then click on your leftside profile-icon option then click on **( configure )** option then find **( API Token )** option then click on *add token* after that generate the token then copy that token and then click apply button and save it and paste your copied token in github-webhook **(secreat )** option and then finally click on **addwebhook** option...


### 👻👻👻👻 HERE YOU DONE WITH YOUR 3RD STEP...LETS MOVE TO 4TH STEP 👻👻👻👻
 
 
### 👉👉👻👻 FROM HERE YOUR CI/CD PIPELINE WORK IS STARTED...BEST OF LUCK 👻👻👈👈
 
#### 👨‍💻After you login on your jenkins dashboard then follow this steps

- 💻Download the plugins of **publish-over-ssh** for access your other servers from jenkins dashboard for that follow the path  click on **( manage-jenkins>>> manage-plugins>>> available-plugin )** then search **publish-over-ssh** plugin and download them 

- 💻Then add your servers in your jenkins, for your jenkins can be access and manipulate your server so 
for that follow this steps **( manage-jenkins>>> configure-system )** then scroll down at last point here you find one option 
that is **( ssh-servers )** then click on add buttton then in **Name** option type **jenkins** and in **( host-name )** option type your **jenkin-server private-ip** then in **( username )** option type **root** ( cause we provide root access to our jenkins of our jenkins-server for perform the task on-behalf your servers ) then clcik on **( advanced )** option then select box of **use password authentication** here in **( password )** option type your **jenkins-server password** whatever you provide password for your root-user then scroll down and click on **test configuration** for test your jenkins can access or not to your server...

- 💻After that click again on "Add" option and add your ansible server also following our earlier instruction and then click on apply and save it...


### 👻👻👻👻 HERE YOU DONE WITH ADDING JENKIN & ANSIBLE SERVER IN YOUR JENKINS SSH-SERVERS DASHBOARD 👻👻👻👻
	  
	     
### 👻👻👻👻 HERE YOU DONE WITH YOUR 4TH STEP...LETS MOVE TO 5TH STEP 👻👻👻👻



//// After that open jenkins dashboard

- Here in jenkins dashboard you have to click on "new item"(that is the pipeline) provide "cicd-project-1" name then select "freestyle project" and click on "okk"

- Then in your "cicd-project-1" job you have to scroll down and find "Source Code Management" option and select "git" then provide git-hub URL (you have to provide that git-hub url whatever devloper are push there source code in it) 

- In "Build Triggers" option choose 'GitHub hook trigger for GITScm polling' for jenkins can automatically triggerd and then build the job as per our set-up

- In "Build steps" option select 'send file or execute commands over ssh'  Then provide jenkins server name whatever you add in ssh-servers so that you have to provide in "name" option (ensure that you have to mention first jenkins server name cause we have to sync that jenkins data to our ansible so for that you have to mention first jenkins server) after that in "exec command" option mention this command 👇👇👇
        $ rsync -avh /var/lib/jenkins/workspace/cicd-project-1/* root@<ansible-server private-ip>:/docker/

- Then in "Post-Build Actions" click on 'add post build actions' then select 'send build artifacts over ssh' then in "name option" type your ansible-server name whatever you add name in jenkins-ssh-servers after that in "exec commands" option copy this commands 👇👇👇 
(we run this commands for build the image from Dockerfile and set the tags to our image for push them to docker-hub then push that image on docker-hub and then remove the remaining docker images from ansible server then the last command is run for the run our ansible-playbook then this ansible playbook create container on your docker server)

    docker build -t $JOB_NAME:V1.$BUILD_ID /home/ubuntu/
    docker image tag $JOB_NAME:V1.$BUILD_ID rohzz024/$JOB_NAME:V1.$BUILD_ID
    docker image tag $JOB_NAME:V1.$BUILD_ID rohzz024/$JOB_NAME:latest 
    docker image push rohzz024/$JOB_NAME:V1.$BUILD_ID
    docker image push rohzz024/$JOB_NAME:latest
    docker rmi $JOB_NAME:V1.$BUILD_ID rohzz024/$JOB_NAME:V1.$BUILD_ID rohzz024/$JOB_NAME:latest
    ansible-playbook /home/ubuntu/project-1.yml

- After that click on "Apply" then "Save"

- Then in your cicd-project-1 job click on "Build Now" and wait for complete your job 


                     👻👻👻👻👻👻 HERE YOU DONE WITH YOUR LAST 5TH STEP BUDDY... 👻👻👻👻👻👻
					 
					 
- Finally your job was completed sucessfully then copy your docker-server public-ip and search them on browser you will see your game and you can play 👻👻👻

-Here if you want to change anything in your source-code then access your devloper server and clone that code in your any local-repo then change anything in index.html file as per your knowledge and commit them then push them on that git-hub repo  after that your jenkins-job automatically get triggerd and you can see your game with whatever you are change in source-code that changes can reflect in your game dashboard 🤩🤩🤩🤩 

NOTE:- IF YOU'RE FACING ANY ISSUE THEM RESEARCH ON IT AND TRY TO SOLVE THAT ISSUE OR ERRORS...GOOD LUCK👍

                   👻👻👻👻 DUDE FINALLY YOU DID THIS PROJECT🥇🥇🥇 AM HAPPY FOR YOU... 👻👻👻👻
					  
					                    👻👻👻👻 THANK YOU 👻👻👻👻
  
  
  

  
  
