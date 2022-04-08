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
