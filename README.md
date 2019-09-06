# kubernetes setup
Requirements:
  - aws account
  - iam user (use this user credentials to login with amazonCli) with the following policies: AmazonEC2FullAccess, AmazonRoute53FullAccess, AmazonS3FullAccess, IAMFullAccess

# There are two ways to setup kubernetes:
  1. using jenkins file document
  2. manual way

# using jenkins file document 
Requirement 
This will be done with 2 steps:
  1. First step is to clone the project url 
  2. Before building up the pipeline project you need to configure jenkinsfile

Note: to run the pipeline script you have to make sure that you have jenkins credentials for aws(with id: kubernetes-aws-client) and ssh connection(with id: KEY_AWS)

# manual way
first we need to be root user

sudo su -

now you have to install awscli in your vm.
login to aws with aws configure and set your, AWS Access Key ID, AWS Secret Access Key, Default region name, Default output format

after login install kubectl and kops in vm. 

# install kops
install kops by running the following commands
  - curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d \'\"\' -f 4)/kops-linux-amd64
  - chmod +x kops-linux-amd64
  - sudo mv kops-linux-amd64 /usr/local/bin/kops

# install kubectl
install kubectl using the following commands
  - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
  - chmod +x ./kubectl
  - sudo mv ./kubectl /usr/local/bin/kubectl

# create s3bucket 
you can create it by the manual way in aws console or by running the following command:
  - aws s3 mb s3://k8s.xlajd.io
  
after creating s3bucket generate a ssh key for kubernetes vm
  - sudo ssh-keygen

Note: before creating the cluster config you need to make sure you have a valind domain to use his id

# create cluster configurations
change command properties with desired cluster values
  - kops create cluster k8s.xlajd.io --node-count 2 --zones us-east-2b \
   --node-size t2.micro --master-size t2.micro \
   --master-volume-size 8 --node-volume-size 8 \
   --ssh-public-key /root/.ssh/id_rsa.pub \
   --state s3://k8s.xlajd.io --dns-zone *(dns ip) --dns private --yes
# start cluster
now you need to simulate your cluster to run
use the following command to start
 - kops update cluster k8s.xlajd.io --state s3://k8s.xlajd.io --yes


