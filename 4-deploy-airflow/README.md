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