# Docker-image-and-container-via-Ansible-playbook
Ansible playbook to build docker image from git repo, then push the to dockerhub, and creating container from that image on the test server.
</br>
</br>

This is a project in which  we implement Docker,Github, Ansible together.</br>
Initially, we set up a website code to Github, so that it can be cloned to a directory for building a zdocker image.</br>
Here, a code written in Python Flask application is stored in Github. </br>
The github repository used here is [Flask app](https://github.com/Haashmi-h/devops-flask)
</br>
</br>
</br>

![Untitled Diagram drawio (1)](https://user-images.githubusercontent.com/117455666/221378259-82a61d18-4487-4eb6-9f06-9da9fb66b1b8.png)


</br> </br> </br>
Basically, it needs 3 servers. We have chosen AmazonLinux servers, each for following purposes:

#### Master 
It act as Ansible Master. This is the server where we insert our source code along with files required to deploy the application.

#### Build
This server is for building Docker image using the contents clonned from [Github](https://github.com/Haashmi-h/devops-flask)

From this server, the newly created Docker image is pushed to the Dockerhub account as a new repository.</br>
For that, Docker hub login credentials are stored in following format inside a file "dockerhub.vars" 
```sh
docker_user: "xxxxxx"
docker_password: "xxxxxx"
```

#### Test
This is the server on which we deploy the Flask application. </br>
Here, we will be creating a Docker container using the Docker image pulled from the Dockerhub repository. </br>
</br>
</br>
Both "build" and "test" servers are categorized as 2 different groups under the Ansible inventory file "hosts". </br>
Ansible playbook "playbook.yml" contains 2 separate tasks to be executed from "build" and "test" servers.


### Set of tasks on "build" server:

1) Installation of following packages: </br>
 `git`
 `pip`
 `docker`

These pakages are invoked as values passed from variables declared using "vars" option.

2) Assigning "ec2-user" to the docker group. </br>
It is needed to perform Docker processes as "ec2-user".</br>

3) Installation of the python extension "docker-py".</br>

4) Restart and enabling Docker service. </br>

5) Clonning Git repo and setup a register module. </br>
The contents from the Github repository will be saved to a directory "/var/flask_app", which is passed as an argument declared before. </br>
Here register module is used since output from the task should be saved and use it later. </br>

6) Build and push image to Dockerhub: </br>
Here, logged in to Dockerhub account using variables passed "via dockerhub.vars". </br>
Built a Docker image from the Flask application contents clonned from Github repository.</br>

7) Removal of local Docker image and logout from Dockerhub.</br>
</br>

### Set of tasks performed on "test" server:

1) Installing packages. </br>
The packages `docker` and `pip` are installed on the server to create Docker container and host application.</br>

2) Adding ec2-user to docker group.</br>
It is needed to perform Docker processes as "ec2-user".</br>

3) Installing python extension for docker "docker-py".</br>

4) Restarting/Enabling Docker service.</br>

5) Docker image pull and Create container. </br>
Docker image is pulled from Dockerhub repository where the image built from "build" server is uploaded. </br>
A container is created using this Docker image. </br>
</br>
</br>
This playbook is executed on the "master" server using following command: </br>

`ansible-playbook -i hosts playbook.yml`

</br>
Once the "Playbook Recap" shows "changed" after completion, the flask application website can be accessed using hostname of the "test" server.</br>
Here is the sample flask application website content:</br>
<img width="448" alt="git-dockrimage-ansible" src="https://user-images.githubusercontent.com/117455666/221378435-c973be8f-3a85-4832-ae51-971ecae41b66.png">


</br>
</br>
</br>

***Thank you***
