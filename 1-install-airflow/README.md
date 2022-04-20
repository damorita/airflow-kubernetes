# airflow-kubernetes
Content found here are code provided by Marc Lamberti's Airflow Udemy courses.
Repository is for training & learning purposes only to familiarize with Apache Airflow set up.
Marc's Github Repo: [airflow-materials-aws](https://github.com/marclamberti/airflow-materials-aws)
## airflow-eks-config
Contains files and cofigurations to be deployed to eks in sections:
- 3-aws-eks-setup
- 4-deploy-airflow
## 1-install-airflow
Section 1... Locally Run an Airflow Instance in Dockker
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
--set git.url=git@github.com:damorita/airflow-kubernetes \
--set git.branch=main \
--namespace flux

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace flux \
--set git.ssh.secretName=flux-git-deploy \
--set git.pollInterval=1m  \
--set chartsSyncInterval=1m \
--set helm.versions=v3

# Validate Install
kubectl get pods -n flux
kubectl logs -f flux-xxxx -n flux
kubectl get namespace

# Install fluxctl
brew install fluxctl
fluxctl version
# Generate deploy keys and add to Github to allow flux to update repo.
fluxctl identity --k8s-fwd-ns flux

```
## 4-deploy-airflow
#### Deploy First Release of Airflow With Helm
```
# After Updating the release yml file, run the fluxctl command to begin sync
fluxctl sync --k8s-fwd-ns flux 

# validate Helm Release Version
helm list --namespace=dev

# validate Pod Updates
kubectl get pods -n dev  

# Log Into Webserver to Run Airflow CLI 
kubectl exec -it airflow-dev-webserver-xxxxxx -n dev -- /bin/bash
airflow dags list

# Run a Dag and Monitor
watch kubectl get pods -n dev #check to see if new pods execute
airflow dags unpause example_bash_operator # run example dag
kubectl delete  pods -l dag_id=example_bash_operator -n dev # delete
```

#### Setting Up Dag Sync With Github
```
# Generate a Secret in K8s to SSH to Github
kubectl create secret generic airflow-git-private-dags -n dev --from-file=gitSshKey=./private-git
```