# Migrating-to-GKE-Containers
Migrating to GKE Containers
sudo apt update && sudo apt -y install apache2
ab -V
#This is ApacheBench, Version 2.3 <$Revision: 1757674 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation,#
git clone https://github.com/GoogleCloudPlatform/gke-migration-to-containers.git
cd gke-migration-to-containers
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a
From the left-hand menu, open the gke-migration-to-containers > terraform > variables.tf.

Then scroll down to the "machine_type" configuration (line 25) and change the machine type from f1-micro to n1-standard-1:

Deployment
make create

Exploring Prime-Flask Environments
Once your resources are ready, it would benefit you to explore the systems.

Run the following to go into the vm-webserver machine that has the application running on host OS:
gcloud compute ssh vm-webserver --zone us-central1-a
Type "Y" when asked if you want to continue. Press Enter twice to not use a passphrase.
List all processes:
ps aux
exit
Run the following to go into the cos-vm machine, where you have docker running the container.
gcloud compute ssh cos-vm --zone us-central1-a
Clone the build files:
git clone https://github.com/GoogleCloudPlatform/gke-migration-to-containers.git
cd gke-migration-to-containers/container
Build the docker image locally with the following command:
sudo docker build -t gcr.io/migration-to-containers/prime-flask:1.0.2 .
Run the following command to get a list of processes running on port 8080:
ps aux | grep 8080
Find the first chronos port number, which will look like the following:
Then run the following command to kill the process on the port number, replacing <YOUR_PORT_NUMBER> with the cron port number you found (in the above example, you would use 763):
sudo kill -9 <YOUR_PORT_NUMBER>
sudo docker run --rm -d --name=appuser -p 8080:8080 gcr.io/migration-to-containers/prime-flask:1.0.2
ps aux
Also notice, if you try to list the python path it does not exist:
ls /usr/local/bin/python
sudo docker ps
sudo docker exec -it $(sudo docker ps |awk '/prime-flask/ {print $1}') ps aux
exit
Get cluster configuration:
gcloud container clusters get-credentials prime-server-cluster
kubectl get pods
kubectl exec $(kubectl get pods -lapp=prime-server -ojsonpath='{.items[].metadata.name}')  -- ps aux
make validate
Load Testing

In Cloud Shell click the + to open a new Cloud Shell tab.
Execute the following, for each resource, replacing [IP_ADDRESS] with the IP address and port from your validation output in the previous step. Note that the Kubernetes deployment runs on port 80, while the other two deployments run on port 8080:

ab -c 120 -t 60  http://<IP_ADDRESS>/prime/10000


In the Debian and COS architectures, horizontal scaling would include:

Creating a load balancer.
Spinning up additional instances.
Registering them with the load balancer.
This is an involved process and is out of scope for this demonstration.

For the third (Kubernetes) deployment, the process is far easier.

In your 1st Cloud Shell tab, run the following:

kubectl scale --replicas 3 deployment/prime-server
After allowing 30 seconds for the replicas to initialize, re-run the load test:

ab -c 120 -t 60  http://<IP_ADDRESS>/prime/10000

Tear Down

Run the following in your 1st Cloud Shell tab:

make teardown

