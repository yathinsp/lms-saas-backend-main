# lms-saas-backend-main
 
**Project file structure** 

 ![structure](https://drive.google.com/file/d/1bqSXAHgUh3QfckM3Ax4_LlAsGXZQI9zq/view?usp=share_link)

 
**Dockerizing the Application:**

Create a new file in the same directory as your Node application, called Dockerfile 

 ![dockerFile](https://drive.google.com/file/d/1R2SEVzNLQ45np7fjc9u3xS4wq6BqF_Ae/view?usp=share_link)

Build the Docker images 

 ![dockerImage](https://drive.google.com/file/d/1P3uFtq4I3z-61yKBwnvYnPG3lxKzB8M2/view?usp=share_link)

The docker images command retrieves the images and tags in the container 

 
 ![dockerTag](https://drive.google.com/file/d/1jedTqJRdow7snLFd6nVRoAflouZubRrL/view?usp=share_link)

  

**Setting up EC2 on AWS**

On AWS Management console navigate to EC2 by searching for it 

 ![i1](https://drive.google.com/file/d/1Ss1wdfjiWk75OLs8C5hcwJmv403NuY7w/view?usp=share_link)

As a next step click on launch instance button,  

 ![i2](https://drive.google.com/file/d/1nDH7_-9sMvIYdjwfG2l4MxaWj1zqul9A/view?usp=share_link)

Next we choose, 

 
![i3](https://drive.google.com/file/d/1MzV_8jAR8ojdHF7_d9pb2Uz11gSb9rHq/view?usp=share_link)
 

Later we choose the t2.micro instance type which is eligible for free tier, so it's recommended to use that 
 
 ![i4](https://drive.google.com/file/d/1QmZqytLSkEIlRY5b0mbEkhY21aL_PlVH/view?usp=share_link)

After launching, 

 
![i5](https://drive.google.com/file/d/1YOfhyp-gjaBfwUX8Ybhd9DEYdlG0qBfq/view?usp=share_link)
 

**Connecting to EC2 instance**

Setting only read permission for root user for the created key-pair file 

![i6](https://drive.google.com/file/d/1eqC07rkq1Jo215qdOjp8dLY66lt7ASQ1/view?usp=share_link) 

Running the docker on port 3000 inside EC2 machine 

 ![i7](https://drive.google.com/file/d/1SMh5iePMgs4Oq4UPOINraVZXh5Cy7AC9/view?usp=share_link)

 
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

![i8](https://drive.google.com/file/d/1kFo82WR1_A0O368CbBi9FJhuDRXugYy5/view?usp=share_link)

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

![](https://drive.google.com/file/d/1h-kz7dHkYCi76gtY4qMn6uAJIkm1BQF7/view?usp=share_link)

After creating a Job we need to create a private and public key which will be used to integrate our GitHub and Jenkins for the Continuous Integration part.

Going back to our instance to create public and private key we need to follow up the following commands.

ssh-keygen
cd .ssh
ls

![](https://drive.google.com/file/d/1H8ga9pmawrVgN-Kz4N8KUiIJsRD5x5ZH/view?usp=share_link)

sudo cat id_rsa.pub
Where id_rsa.pub stands for Public Key and id_rsa stands for Private Key

After generating the Public and Private Key now we need to connect our Jenkins with Github for that we need to create an SSH KEY.

Go the Github Settings > click on SSH and GPG keys > Give any title and paste the Public Key we generated > click on Add SSH Key

![](https://drive.google.com/file/d/1WUfdMnh8D4fVtAHYOcOIbUUSn1NUYmwz/view?usp=share_link)

After creating the freestyle Project now we need to add the respective credentials in the Jenkins

![](https://drive.google.com/file/d/1_iFg-UPk9dx-ZijzUQXMhG2OAkVwax41/view?usp=share_link)

Give the Jenkins Project a Description after that add the Repo url "Github Link"

In the Source Code Management, choose git and enter the repository URL, by clicking on code.

![](https://drive.google.com/file/d/1BERkJgZlTWEQY8AQTwOic6fSQIAMqh0-/view?usp=share_link)

Create a new credential by clicking on add and new pop-up screen will appear overhere Select kind to be "SSH Username with private key", let the scope be global.

![](https://drive.google.com/file/d/1EmF-FY42Qzqx_gW7E1k_cDULkBWZn48z/view?usp=share_link)

Further, add the Private Key generate in the instance and paste it over here and click on add button to save it.

![](https://drive.google.com/file/d/1U2bA8cRHUNw84bo4E29b52SL-MlKRtui/view?usp=share_link)

After select the ubuntu credentials and Since all the code is in the master branch in the given project, we will use the master branch.

![](https://drive.google.com/file/d/17sIjp5q-N3KW8U6xhea_8uKyV9Ezby4g/view?usp=share_link)

Here click on the "Github hook trigger for GITScm polling" tick is so the any chance made in the Github code base can be reflect over here and a new build can be executed for that we need to make a small change in our Project Repository and all this work is done by Github Webhook > Go to Project Repo Settings > Webhook > Add webhook

![](https://drive.google.com/file/d/14dqffntjuX3MAoLtr6unwvcpsxrdgFev/view?usp=share_link)

Over their you need to enter your this IP address with the port number, i.e., "<ip_address:port_number/github-webhook/>"- "http://3.85.112.241:8080/github-webhook/" and select content type as "application/json" and click on "Add webhook"

![](https://drive.google.com/file/d/1-rLOtkZVpQrPFZNpYNIMmPf0cNpMiu8i/view?usp=share_link)

Architecture diagram

![](https://drive.google.com/file/d/1M_5yTAB_nF-qJwjWLywkLLuhSoGfwWty/view?usp=share_link)