=================================================
Step 1:
Virtual environment AWS
=================================================
go to EC2
click on EC2 launch instance 
Name the Instance 
Choose Amazon Linux OS Image (AMI)
Choose t2.medium 
proceed without the key
launch the instance
under security tab add the inbound rule to all trafic all port
and connect the instance
=================================================
Step 2:
Install Docker
=================================================
sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker
docker ps
docker login
sudo systemctl start docker
=================================================
Step 3: 
Install Nginx
=================================================
docker search nginx
docker pull nginx
=================================================
Step 4:
Install Git
=================================================
sudo yum install git -y
git –version
git config --global user.name "rainabanakar1994"
git config --global user.email “rainabanakar7@gmail.com”
=================================================
step 5:
Install Jenkins:
=================================================
Add jenkisn repo
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/rpm-stable/jenkins.repo
import key from jenkisn CI
sudo rpm --import https://pkg.jenkins.io/rpm-stable/jenkins.io-2026.key
sudo yum upgrade
install java
sudo yum install java-21-amazon-corretto -y
install jenkins
sudo yum install jenkins -y
give permission to jenkins (usergroup permission to jenkins)
sudo usermod -aG docker Jenkins
enable jenkisn service
sudo systemctl enable Jenkins
start jenkins
sudo systemctl start Jenkins
get access to jenkisn UI
open url in browser http://13.63.241.244:8080/
ulock jenkins by giving the key obtained from the command below
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
copy the key to the UI – AUTO installs
create username password in Jenkins UI
check with samplejob build 
new item create
create a new job
select pipeline and give a name to the pipeline
select configure
select triggers 
select github hook trigger for GITScm polling (to trigger github plugin when changes are pushed to github)
under pipeline select pipeline script from SCM under SCM select git 
give the repo URL
under branch - develop
and save
under setting select credentials 
and give docker credentials 
=================================================
step 6:
Install Kubernetes
==================================================
Install Kubectl
curl -LO https://dl.k8s.io/release/v1.30.1/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version –client
Install minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
minikube version
minikube start --driver=docker
kubectl get pods
==================================================
step 7:
kube config to access Jenkins – access permissions
==================================================

sudo mkdir -p /var/lib/jenkins/.kube
sudo cp ~/.kube/config /var/lib/jenkins/.kube/config
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
sudo cp -r ~/.minikube /var/lib/jenkins/.minikube
sudo chown -R jenkins:jenkins /var/lib/jenkins/.minikube
sudo sed -i "s|/home/ubuntu|/var/lib/jenkins|g" /var/lib/jenkins/.kube/config


sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
sudo chmod 700 /var/lib/jenkins/.kube
sudo chmod 600 /var/lib/jenkins/.kube/config


sudo -u jenkins kubectl --kubeconfig=/var/lib/jenkins/.kube/config get nodes

1. Replace the old home path with Jenkins home
sudo sed -i 's|/home/ec2-user|/var/lib/jenkins|g' /var/lib/jenkins/.kube/config
2. Verify it changed
sudo grep -E "certificate-authority|client-certificate|client-key" /var/lib/jenkins/.kube/config
You should now see:
/var/lib/jenkins/.minikube/...
and not /home/ec2-user/....
3. Make sure the files exist
sudo ls -l /var/lib/jenkins/.minikube/ca.crt
sudo ls -l /var/lib/jenkins/.minikube/profiles/minikube/client.crt
sudo ls -l /var/lib/jenkins/.minikube/profiles/minikube/client.key
4. Fix ownership
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube /var/lib/jenkins/.minikube
5. Test exactly as Jenkins
sudo -u jenkins kubectl --kubeconfig=/var/lib/jenkins/.kube/config get nodes

[ec2-user@ip-172-31-43-41 ~]$ kubectl config view --raw --flatten > kubeconfig-jenkins.yaml
[ec2-user@ip-172-31-43-41 ~]$ nano kubeconfig-jenkins.yaml
Replace with     server: https://127.0.0.1:32769
kubectl --kubeconfig=kubeconfig-jenkins.yaml get nodes
==================================================
Step 8:
create index.html
==================================================
create a new directory
then go inside the directory
create index.html
then create a dockerfile
create nginx.conf and expose 8081 inside it
create a jenkinsfile
create k8s folder insde this directory
create deployment.yaml and service.yaml
==================================================
Step 9:
create index.html
==================================================
login to github
create a repo (dev and main)
inside dev - copy the git repo 
git remote add the url 
git commit 
git push -u origin develop 
==================================================
Step 10:
start minikube
==================================================
minikube start --driver=docker
under jenkins pipeline build a pipeline
if the build is succesful add webhooks in github
==================================================
Step 11:
Webhooks
==================================================
go to github repository
select settings
select webhooks
payload URL (http://3.87.133.37:8080)
conent type: application/json




