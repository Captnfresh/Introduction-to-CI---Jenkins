# Introduction to Jenkins

In previous Project, we introduced horizontal scalability concept, which allow us to add new Web Servers to our Tooling Website and we have successfully deployed a set up with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers - it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is Automation of routine tasks.

In this project we are going to start automating part of our routine tasks with a free and open source automation server - Jenkins. It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

Acording to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In this project, we will utilize Jenkins' CI capabilities to ensure that every change made to the source code in the GitHub repository is automatically updated on the Tooling Website.

## Task 

Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look upon competion of this project:

![image 1](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%201.jpg)


## Step 1 - Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 24.04 LTS and name it "Jenkins"

2. Install JDK (since Jenkins is a Java-based application).

   ```
   sudo apt update
   sudo apt install default-jdk-headless
   ```

3. Install Jenkins:

   ```
   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
   https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
   https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
   /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt-get update
   sudo apt-get install jenkins
   ```

4. Make sure Jenkins is up and running:

   ```
   sudo systemctl status jenkins
   ```

5. Configure Security Group. By default, Jenkins uses TCP port 8080. Open this port by creating a new inbound rule in your EC2 Security Group

![image 2](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%202.jpg)

6. Access Jenkins in your browser using http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080.

![image 3](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%203.jpg)

7. You will be prompted to provide a default admin password. Retrieve it from your server:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

![image 4](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%204.jpg)

![image 5](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%205.jpg)

9. Install the suggested plugins when prompted.   

10. Create an admin user.

![image 6](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%206.jpg)

11. You will be provided with your Jenkins server address upon completing the setup.

![image 7](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%207.jpg)

![image 8](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%208.jpg)



## Step 2 - Configure Jenkins to Retrieve Source Codes from GitHub Using Webhooks.
In this part, we will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will be triggered by GitHub webhooks and will execute a 'build' task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable Webhooks in GitHub by navigating to your GitHub repository settings and enable webhooks to trigger Jenkins builds on new commits.

![image 9](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%209.jpg)

### NB : Payload URL is http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080/github-webhook/.

![image 10](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2010.jpg)


2. Create a New Jenkins Project by accessing the Jenkins web console, Click "New Item" and create a "Freestyle project".

![image 11](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2011.jpg)

3. Configure Jenkins Project to Use GitHub Repository. In the project configuration, select Git as the source code management tool.

4. Provide the link to your Tooling GitHub repository and credentials (username and password) to allow Jenkins to access files in the repository and save the configuration.

![image 12](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2012.jpg)

5. Run the Build Manually
* Click "Build Now" to manually trigger the build.
* If configured correctly, the build should be successful, and you will see it under Build #1.
* Open the build and check the "Console Output" to ensure the build ran successfully.

![image 13](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2013.jpg)

6. Configure Automatic Builds with GitHub Webhook
* Go back to your Jenkins project and click "Configure".
* Under the "Build Triggers" section, select "GitHub hook trigger for GITScm polling".
* This configuration ensures that any change made in your GitHub repository will trigger a new Jenkins build.

7. Configure Post-build Actions. Add a "Post-build Action" to archive all files. Files resulting from a build are called artifacts. Under the form field for Files to archive, use ** to select all.

8. Test Automatic Build Trigger
* Make some changes in any file within your GitHub repository (e.g., README.md) and push the changes to the main branch.
* You should see that a new build is launched automatically by the webhook, and you can see the results stored on the Jenkins server.

![image 14](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2014.jpg)

## CONGRATULATIONS!!!! 
 We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as 'push' because the changes are being 'pushed' and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.  


By default, Jenkins stores the build artifacts locally on the Jenkins server.

```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```
![image 15](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2015.jpg)


## Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory. Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1. Install "Publish Over SSH" plugin.
On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item. On "Available" tab search for "Publish Over SSH" plugin and install it.

2. Configure the job/project to copy artifacts over to NFS server.
On main dashboard select "Manage Jenkins" and choose "Configure System" menu item. Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to our NFS server. Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

3. Paste the private here:
   * Arbitrary name
   
   * Hostname - can be private IP address of your NFS server.
   
   * Username - ec2-user (since NFS server is based on EC2 with RHEL 8).
   
   * Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

4. Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections. Save the configuration, open Jenkins job/project configuration page and add another one "Post-build Action"

5. Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use **. If you want to apply some particular pattern to define which files to send - use this syntax.

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

![image 16](https://github.com/Captnfresh/Introduction-to-CI---Jenkins/blob/main/Introduction%20to%20Jenkins/image%2016.jpg)

### Conclusion:

This project demonstrates the successful implementation of a Continuous Integration and Continuous Deployment (CI/CD) pipeline for a tooling website using Jenkins. By automating the deployment process, we have significantly improved the efficiency and reliability of our software delivery workflow.

Key achievements of this project include: Setting up a Jenkins server to orchestrate the CI/CD pipeline. Integrating version control (Git) with Jenkins for automated builds and deployments. Implementing automated testing to ensure code quality and reduce the risk of bugs in production. Configuring Jenkins to deploy the tooling website to a production environment automatically. Demonstrating the benefits of CI/CD practices in terms of faster release cycles and improved collaboration among team members. This project serves as an excellent introduction to Jenkins and CI/CD concepts, providing a solid foundation for further exploration of advanced DevOps practices. By adopting these automation techniques, we have taken a significant step towards more efficient and reliable software development and deployment processes.



  

        





















