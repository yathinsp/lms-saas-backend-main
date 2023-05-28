# lms-saas-backend-main
 
**Project file structure** 

 ![Screenshot 2023-05-06 071303](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/08232ca0-12e0-4cd0-989a-5ee52e22e87f)

**Dockerizing the Application:**

Create a new file in the same directory as your Node application, called Dockerfile 

![Screenshot 2023-05-06 071903](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/cb811967-9726-4fef-be99-4f4457b1e65c)


Build the Docker images 

![Screenshot 2023-05-06 072349](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/271588bc-753f-463b-84bd-ecd6a5c99c8c)

The docker images command retrieves the images and tags in the container 

   ![Screenshot 2023-05-06 072534](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/39ce3bbf-f472-4f04-ac63-8f5c978598a0)


**Setting up EC2 on AWS**

On AWS Management console navigate to EC2 by searching for it 

![Screenshot 2023-05-06 072748](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/2e99ec7b-4daa-4e1d-954a-0ea00351185e)

As a next step click on launch instance button,  

 ![Screenshot 2023-05-06 072901](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/45f7b044-0f88-4c66-9c03-9dcd44c9df93)

Next we choose, 

![Screenshot 2023-05-06 073046](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/8c848b28-3e22-4d34-ba6e-7c4a60187948)
 

Later we choose the t2.micro instance type which is eligible for free tier, so it's recommended to use that 
 
![Screenshot 2023-05-06 073232](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/7ba7f43e-bde1-43bf-a045-4d48799b976b)


After launching, 

 ![Screenshot 2023-05-06 073343](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/58b3f11a-a923-489a-b684-960eeb249e06)
 

**Connecting to EC2 instance**

Setting only read permission for root user for the created key-pair file 

![Screenshot 2023-05-06 073515](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/4ab2b3e9-945b-4f9d-bb47-803b4d1f271e)

Running the docker on port 3000 inside EC2 machine 


![Screenshot 2023-05-06 074015](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/dd8fad8d-65de-4331-98fc-0b8aca2bed9e)

 
**Installing Jenkins on the Instance**
We are using sudo it as a prefix with every command, you can also do sudo -su this in the beginning to avoid that.

Enter the command, for a quick update of all the software packages on your instance

_Update your system_

  sudo apt update

_Install java_

  sudo apt install openjdk-11-jre

_Validate installation_

  java -version

It should look something like this

  openjdk version "11.0.12" 2021-07-20 OpenJDK Runtime Environment (build 11.0.12+7-post-Debian-2) OpenJDK 64-Bit Server VM (build 11.0.12+7-post-Debian-2, mixed mode, sharing)

**Install Jenkins**

Just copy these commands and paste them onto your terminal.

  curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null

  echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | 
  sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null

  sudo apt-get update

  sudo apt-get install jenkins

_Start Jenkins_

  sudo systemctl enable jenkins

  sudo systemctl start jenkins

  sudo systemctl status jenkins


Now copy the public IP address of the Jenkins instance, which is present in the details of the instance

Enter this IP address with the port number, i.e., "<ip_address:port_number>"- "http://3.85.112.241:8080"

![Screenshot 2023-05-06 074415](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/425536b2-0c8b-463a-a983-36c7d3ba5f49)


Get the password by entering the following command and enter it in the text box

    sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Click on install the suggested plugins

**Creating a Container**

After running Jenkins on our host now is time to create a Dockerfile to create a Dockerfile we need to install it first docker in our instance.

  sudo apt install docker.io
  sudo usermod -a -G docker $USER

  FROM node:12.2.0-alpine
  WORKDIR app
  COPY . .
  RUN npm install
  EXPOSE 8000
  CMD ["node","app.js"]

Now its time to make docker image and container out of the docker file

  sudo docker build . -t node-app

  sudo docker run -d -p 8000:8000 todo-node-app

To see our Project running we need to add port in our security group of the instance

Follow the same steps, as followed in adding an inbound port for Jenkins, with some minor changes.

One inbound rule need to be added

Automating CI/CD using Jenkins

First, you need to create a New Jenkins freestyle Pipeline

![Screenshot (462)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/00c77cbd-8341-44fc-8e85-73e9cff87cb8)


After creating a Job we need to create a private and public key which will be used to integrate our GitHub and Jenkins for the Continuous Integration part.

Going back to our instance to create public and private key we need to follow up the following commands.

ssh-keygen
cd .ssh
ls

![Screenshot 2023-05-28 174835](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/9170d161-1523-4885-9831-144ea526ed5c)

sudo cat id_rsa.pub
Where id_rsa.pub stands for Public Key and id_rsa stands for Private Key

After generating the Public and Private Key now we need to connect our Jenkins with Github for that we need to create an SSH KEY.

Go the Github Settings > click on SSH and GPG keys > Give any title and paste the Public Key we generated > click on Add SSH Key

![Screenshot (463)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/f3c640f2-519c-4e44-89c0-6338ffaee2a5)


After creating the freestyle Project now we need to add the respective credentials in the Jenkins

![Screenshot (464)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/1af63a95-fbc8-4c03-b2ce-20c755ec74eb)


Give the Jenkins Project a Description after that add the Repo url "Github Link"

In the Source Code Management, choose git and enter the repository URL, by clicking on code.

![Screenshot (465)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/0ea417f3-aa68-46f6-81ee-c625080c817c)


Create a new credential by clicking on add and new pop-up screen will appear overhere Select kind to be "SSH Username with private key", let the scope be global.

![Screenshot (466)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/a720eded-9da7-4fed-83e0-91844d1abe37)


Further, add the Private Key generate in the instance and paste it over here and click on add button to save it.

![Screenshot (468)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/315d180d-f30e-4a0c-b93b-824855571e33)


After select the ubuntu credentials and Since all the code is in the master branch in the given project, we will use the master branch.

![Screenshot (469)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/406dbcda-6b84-4eb7-8c4b-93604ec9ebdc)


Here click on the "Github hook trigger for GITScm polling" tick is so the any chance made in the Github code base can be reflect over here and a new build can be executed for that we need to make a small change in our Project Repository and all this work is done by Github Webhook > Go to Project Repo Settings > Webhook > Add webhook

![Screenshot (470)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/da248754-ea03-4e61-b8b3-317a394f661e)


Over their you need to enter your this IP address with the port number, i.e., "<ip_address:port_number/github-webhook/>"- "http://3.85.112.241:8080/github-webhook/" and select content type as "application/json" and click on "Add webhook"

![Screenshot (471)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/c69c613e-5914-4ba1-836d-413258005378)

![Screenshot (472)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/b2c7d23d-b354-409f-8f0d-2c63e9b8299c)

![Screenshot (473)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/f7ab5be3-5264-4a82-98be-7333f865f1ce)

![Screenshot (474)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/07d7925b-402f-4301-b4d1-28aaeeed2d67)

![Screenshot (475)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/2f89ce1f-da8f-4b1c-964a-908b4d504ada)

![Screenshot (476)](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/a4f6c963-f691-4232-8894-07d3fccc93d2)


**CloudWatch Monitoring**

To get started with CloudWatch Application Insights from the CloudWatch console, perform the following steps.

Start. Open the CloudWatch console landing page. From the left navigation pane, under Insights, choose Application Insights. The page that opens shows the list of applications that are monitored with CloudWatch Application Insights, along with their monitoring status.

Add an application. To set up monitoring for your application, choose Add an application. When you choose Add an application, you are prompted to Choose Application Type.

Resource group-based application. When you select this option, you can choose which resource groups in this account to monitor.

Account-based application. When you select this option, you can monitor all of the resources in this account. If you want to monitor all of the resources in an account, we recommend this option over the resource group-based option because the application onboarding process is faster.

Note
You cannot combine resource group-based monitoring with account-based monitoring using Application Insights. In order to change the application type, you must delete all of the applications that are being monitored, and Choose Application Type.

When you add your first application for monitoring, CloudWatch Application Insights creates a service-linked role in your account, which gives Application Insights permissions to call other AWS services on your behalf. For more information about the service-linked role created in your account by Application Insights, see Using service-linked roles for CloudWatch Application Insights.



Resource-based application monitoring

Account-based application monitoring

Select resource group. On the Specify application details page, select the AWS resource group that contains your application resources from the dropdown list. These resources include front-end servers, load balancers, auto scaling groups, and database servers.

If you have not created a resource group for your application, you can create one by choosing Create new resource group. For more information about creating resource groups, see the AWS Resource Groups User Guide.

Monitor CloudWatch Events. Select the check box to integrate Application Insights monitoring with CloudWatch Events to get insights from Amazon EBS, Amazon EC2, AWS CodeDeploy, Amazon ECS, AWS Health APIs And Notifications, Amazon RDS, Amazon S3, and AWS Step Functions.

Integrate with AWS Systems Manager OpsCenter. To view and get notified when problems are detected for selected applications, select the Generate Systems Manager OpsCenter OpsItems for remedial actions check box. To track the operations that are taken to resolve operational work items (OpsItems) that are related to your AWS resources, provide the SNS topic ARN.

Tags — optional. CloudWatch Application Insights supports both tag-based and CloudFormation-based resource groups (with the exception of Auto Scaling groups). For more information, see Working with Tag Editor.

Choose Next.

An ARN is generated for the application in the following format.


arn:partition:applicationinsights:region:account-id:application/resource-group/resource-group-name
Example


arn:aws:applicationinsights:us-east-1:123456789012:application/resource-group/my-resource-group
After you submit your application monitoring configuration, you will be taken to the details page for the application, where you can view the Application summary, the list of Monitored components and Unmonitored components, and, by selecting the tabs next to Components, the Configuration history, Log patterns, and any Tags that you have applied.

To view insights for the application, choose View Insights.

You can update your selections for CloudWatch Events monitoring and integration with AWS Systems Manager OpsCenter by choosing Edit.

Under Components, you can select the Actions menu to Create, Modify, or Ungroup an instance group.

You can manage monitoring for components, including application tier, log groups, event logs, metrics, and custom alarms, by selecting the bullet next to a component and choosing Manage monitoring.

Enable Application Insights for Amazon ECS and Amazon EKS resource monitoring
You can enable Application Insights to monitor containerized applications and microservices from the Container Insights console. Application Insights supports monitoring for the following resources:

Amazon ECS clusters

Amazon ECS services

Amazon ECS tasks

Amazon EKS clusters

When Application Insights is enabled, it provides recommended metrics and logs, detects potential problems, generates CloudWatch Events, and creates automatic dashboards for your containerized applications and microservices.

You can enable Application Insights for containerized resources from the Container Insights or Application Insights consoles.

Enable Application Insights from the Container Insights console
From the Container Insights console, on the Container Insights Performance monitoring dashboard, choose Auto-configure Application Insights. When Application Insights is enabled, it displays details about detected problems.

Enable Application Insights from the Application Insights console
When ECS clusters appear in the component list, Application Insights automatically enables additional container monitoring with Container Insights.

For EKS clusters, you can enable additional monitoring with Container Insights to provide diagnostics information, such as container restart failures, to help you isolate and resolve problems. Additional steps are required to set up Container Insights for EKS. For information, see Setting up Container Insights on Amazon EKS and Kubernetes for steps to set up Container Insights on EKS.

Additional monitoring for EKS with Container Insights is supported on Linux instances with EKS.

**Architecture diagram**

![image](https://github.com/yathinsp/lms-saas-backend-main/assets/38605313/7409f570-45f6-43e2-a6af-72a2f07d75b0)

