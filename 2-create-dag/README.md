
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
