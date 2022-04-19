# airflow-kubernetes
Content found here are code provided by Marc Lamberti's Airflow Udemy courses.
Repository is for training & learning purposes only to familiarize with Apache Airflow set up.

## 1-install-airflow
Section 1... Creating an airflow instances with Docker


## 2-create-dag
Section 2... Set up a sample dag to transform forex data to a hive database and then send notification

Folders:
    - docker: Dockerfile for dependent database, hive & spark instances
    - mnt: File paths to be mounted to docker contianers during run
    - scripts: start.sh (start all containers), stop.sh (stop all containers), reset.sh (reset all configurations), restart.sh (stop & start containers)

Dag Includes Steps for:
    - HttpSensor
    - FileSensor
    - PythonOperator
    - BashOperator
    - HiveOperator
    - SparkSubmitOperator
    - EmailOperator
    - SlackWebhookOperator

## 3-aws-eks-setup
#### Set up AWS CLI
```
aws configure
aws sts get-caller-identity
export ACCOUNT_ID=xxxxxx
export AWS_REGION=xx-xxxx-#
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```
#### Set up SSH Keys to allow programmatic access to Repo
```
#Generate your keys locally
ssh-keygen -t rsa 

# import ssh keys to AWS from keys from above
aws ec2 import-key-pair --key-name "airflow-workstation" --public-key-material fileb:///your/ssh/keys/file.pub

```
#### Create EKS Cluster
```
eksctl create cluster -f cluster.yml
# Clean up: eksctl delete cluster --wait --name=airflow
```

#### Installing Flux & Operator Config to Monitor Repo
```
kubectl create namespace flux
helm repo add fluxcd https://charts.fluxcd.io

helm upgrade -i flux fluxcd/flux \
--set git.url=git@github.com@damorita/airflow-kubernetes \
--namespace flux

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace flux \
--set git.ssh.secretName=flux-git-deploy \
--set git.pollInterval=1m  \
--set chartsSyncInterval=1m \
--set helm.versions=v3

# Validate Install
kubectl get pods -n flux

# Install fluxctl
brew install fluxctl
fluxctl version
# Generate deploy keys and add to Github
fluxctl identity --k8s-fwd-ns flux

```