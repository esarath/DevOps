# springboot-app

Pre-requistes:
1. AKS cluster needs to be up running. You can create AKS cluster using any of one of the below options:


Create AKS cluster in Azure portal directly
Create AKS cluster using Azure CLI (refer AKS repositary: https://github.com/esarath/AKS/tree/main/Create%20AKS%20using%20azure%20CLI )
Create AKS cluster using Terraform (refer terraform repositary: https://github.com/esarath/Terraform/tree/main/AKS_Cluster )
2. ACR is also setup in Azure cloud. 
3. Have Azure DevOps project dashboard in 
       https://dev.azure.com/
4. Dockerfile created along with the application source code for springboot App.
5. Make sure AKS has pull access from ACR
6. Modify K8S manifest file per acr, image name for AKS Deployment

Implementation Steps:

STEP 1 - Create Azure Build pipeline for building Docker images and uploading into ACR
STEP 2 - Create Azure Release pipeline for deploying Springboot Docker containers into AKS
 
Follow STEP 1 - How to create a Azure Build Pipeline

1. Login into your Azure DevOps dashboard
2. Click on Pipelines
3. Click on New Pipeline
4. Click on use the classic editor

Enter your repo name and branch name where you have stored your source code along with Dockerfile:

Click on Continue. Now choose the template by typing Docker, Select Docker container and Apply.

Now pipeline is created with two tasks already. We need to more tasks:
Let's add Maven build task for building the JAR file.
Click on + icon and type Maven
And then enter maven goal as package

Let's modify Build an image task.
Select Push an image task
Add a task for Copying YAML file, enter the Kubernetes deployment YAML file - aks-deploy-from-acr.yaml

Add Publish artifact task
Now click Save + Queue and run to start Building the pipeline
Once the build is completed, you should be able to see the Docker images under 
Services --> Repositories

Follow STEP 2 - How to Create Release pipeline for deploying Docker containers into AKS Cluster 
Go to Pipelines --> Click on Releases --> New Release pipeline
Click on Stage 1 and choose a template by selecting
Deploy to a Kubernetes cluster and click on Apply
Change the stage name to Deploy to AKS or Any name

Now click on Add an artifact
Select the Build pipeline and click on the latest version
Now click on Deploy to AKS stage

Add Replace token task
Click on + to add task, type token and choose replace token task. 
Now click on replace token task and Clik on root directory, click on ... dots
 select the drop directory from below:
 and enter Target file as  "aks-deploy-from-acr.yaml"
 
 Click on kubectl apply
 Now Click on New to enter AKS cluster connection info
 Choose the Azure subscription and enter Microsoft user credentials.
 Select AKS cluster from the drop down, choose default namespace
 Choose command as apply and select the yaml file from the dropdown from Configuration file 
 Now click on Save,
Click on Create a release
and then click Create to run the deployment

Click on Stage to see the logs
Now you will see the following tasks are in green to confirm Deployment was successful.
Let's check if deployment created any pods

  kubectl get nodes
	kubectl get deployments
	kubectl get pods
	kubectl get svc
	kubectl logs <pod_name>
	
	Now try to access spring boot application running inside AKS cluster by using external IP and port number

  If you see any errors after deploying the pods, you can check the pod logs.
    
    kubectl logs <pod_name>

  Go to the browser enter http://external IP
  

  










