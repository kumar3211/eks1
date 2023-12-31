## Create an user in IAM and provide privilises for EKS access 
## configure AWS CLI 
## Install EKS CTL tool
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

##  Create an EKS cluster with empty node group

eksctl create cluster --name=sample-pattanvcube --region=us-east-1 --version 1.24 --zones=us-east-1a,us-east-1b --without-nodegroup 

## Create & Associate IAM OIDC Provider for our EKS Cluster
To enable and use AWS IAM roles for Kubernetes service accounts on our EKS cluster, we must create & associate OIDC identity provider.

eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster sample-pattanvcube --approve

## Create an EC2 key pair Or take existing key pair (myweb9999)

## Create Node Group with additional Add-Ons in Public Subnets
It will help us to create IAM policys automatically

eksctl create nodegroup --cluster=10ambatch \
                       --region=us-east-1 \
                       --name=10ambatch-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=1 \
                       --nodes-min=1 \
                       --nodes-max=4 \
                       --node-volume-size=8 \
                       --ssh-access \
                       --ssh-public-key=k8s \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access 
					   
## Verify the subnets having connectivity (keep all traffic inbound rules for all SecurityGroups)

##Please make sure webserver should be up and running for all workdernodes while access application in browser

## install kubectl for compatability version eg : mine is using 1.21.14 

curl -LO https://dl.k8s.io/release/v1.21.14/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client


## Get the config for cluster 
aws eks update-kubeconfig --region us-east-1 --name sample-ekscluster
kubectl get nodes 
kubectl get nodes -o wide 

## Get capacity of pods for running in cluster 
kubectl get node -o yaml | grep pods



aws eks update-kubeconfig --region us-east-1 --name sample-vmrvcube
