# cloud-native-monitoring-app
Cloud Native app on K8S to monitor system resources using python

Pipleline:

 **GITHUB** - SCM -> **Jenkins** - CI -> **Docker** - image creation -> **Docker HUB** - Pushing artifact -> **Shellscript** - to update the deployment yaml file -> **ArgoCD** - CD -> **Kubernetes**

What is this project about?

1. Deploying a containerized python application which will simply check the CPU utilization and Memory utilization of the container in a K8s cluster using Argo cd.
2. Project flow:
     - Get the image from Git repository
     - Building the docker image
     - Pushing the image to the docker hub repository(both private/public)
     - Running a shell script to update the app name and app version in the deployment file
     - Once done, the argocd in the kubernetes cluster will deploy the containers and prune the previously deployed containers
  
3. Purpose of the project:
     - To implement the ArgoCD gitops
     - ArgoCD continuosly check for any code change in the repository for the deploy.yaml and appservice.yaml files every 3 minutes.
     - With this ArgoCD deployments, we dont want to enter any commands in the cluster manually since the argocd is a deployed service which runs in the cluster, it will automatically
       fetch the updates and create a new deployment in notime.
     - With ArgoCD we can have the cluster secured and highly available.
