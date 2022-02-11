Repository Overview / Project Introduction

In this project, our playbook will start a minikube if it's not running, build the http_server ruby app image if it's not available at local, then deploy the app to our minikube cluster. Then, it will expose the pod via a load balancer service and will access the app. Find folder structure below:

├── apps
|    ├── /http_server
|    |    ├── http_server.rb
|    ├── Dockerfile
├── /kubernetes
|    ├── deployments.yaml
|    ├── services.yaml
├── hosts
├── main.yaml

What you'll need
- Minikube
- Docker(Preferrably Docker desktop)
- Kubectl
- Virtual Machine (Vmware workstation/fusion or Virtual Box)

System Requirements
- 2 CPUs or more
- 2GB of free memory
- 20GB of free disk space
- Internet connection

Step-by-Step guide
Minikube
1. Download, Install and Run minikube from official guide here https://minikube.sigs.k8s.io/docs/start/

Dockerfile
1. Ensure you have Docker installed on your local machine by running, Docker --version 
2. Create a new folder 'apps' for the copied source code(/http_server) and move it to /apps directory.  
3. In that folder create Dockerfile with ruby-alpine base-image to reduce overall container size. 
4. Update the Image OS and set a working directory to /home/myapp
5. Add a new group and internal user  and giver permissions for only the user to run my app folder incase someone wrongly gains access to the docker image.
6. Switch to new user and copy the project file into the work directory
7. Expose port 80 for incoming requests 
8. Run the ruby image with specified command
9. In the Dokcerfile folder, build the image with, Docker build -t . 

Kubernetes
1. Ensure you have Installed Minikube for local kubernetes setup
2. Create Kubernetes Deployment(4 replicas) and Services file as seen in /kubernetes . 
3. In the deployment file create a resource limit for CPU(150m) and RAM(128Mb) to ensure no container hugs resources.
3. In the deployment file create an a container with a liveness probe and readiness probes to ensure the container starts responding before recieving traffic, adding to your organizations high availabilty requirements.   
4. Create a Loadbalaner services as seen in the yaml file

Ansible
1. Create an ansible playbook called main.yaml  
2. Create a hosts file with localhost as seen in the root directory
3. main.yaml file steps
    - Add possible variables as seen in the file 
    - Add pre-tasks to check if minikube is running, and if not, to start minikube
    - Start the main tasks by retrieving the docker image and building from scratch if not available on the minikube VM.  
    - Add task to run the WebApp kubernetes deployment and Service files
    - Scale deployment to required number of replicas
    - Expose the Web app on the host via Minikube.

How to connect
Since we are using the loadbalancer type, minikube has to tunnel a route to services sets their Ingress to the ClusterIP. 

If you type 'kubectl get svc' you'll notice the IP for service type loadbalancer is in "pending" state

To allow load balancer access to your external host open a new terminal window(as you cannot exit the process) and input "minikube tunnel". You can check your services again and get the IP to be used on your browser. You should be able to sucessfully connect now. 

Non-Current Software Verion Use
- http0.9: This had to be used in the curl command for liveness and readiness probes as the current http version doesn't handle the requests returning a direct text of "OK"