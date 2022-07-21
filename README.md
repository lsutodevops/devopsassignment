For step one, two Files were created one for the the client image and one for the the api image. Gunicorn was added to the api image to serve the Flask application.
Nginx was added to the React client image to forward reqeusts to the api container.

The compose file itself builds each image again when `compose up` is executed. `docker-compose up --build` works also. The React image uses yarn to build each time.

A modification was made in the App.js application (replace localhost in the line indicated below) so that it would work well with Compose and Nginx on the client image.
Compose assigns the api hostname/ip during deployment. Having localhost here makes it difficult to configure Nginx to forward when deployed to cloud containers and Kubernetes.

	const res = await fetch('/api/stats');

The 'expose:' section is used in the compose file as it allows the api container to only allow connection from the client container.

------------------------------------------------------------------------------------------------------------------------------------------------

For step two, the containers were deployed to a container group in Azure Container Instances using the Docker Compose integration. 

Deployment are steps detailed here:
https://docs.microsoft.com/en-us/azure/container-instances/tutorial-docker-compose

Its deployed here http://52.149.62.224:80

To upload images to the Azure Container Registry update the image property in the Docker Compose service section as follows: Prefix the image name with the login server name of your Azure container registry, <acrName>.azurecr.io
In this deployment (from the docker-compose.yml: acrdpoc.azurecr.io/react_app_flask)


Currently it is uncertain how to set the public ip to static so it will change whenever the app is redeployed.
It does run in a specific ACI container group that is generated dynamically. Once deployed and if you are attached to the docker aci context, you can get the 
public ip of the client container with docker ps. 


It is also deployed as Docker Compose application on an Ubuntu VM in Azure at this IP:
The Compose file 'docker-compose-kube.yml' was created for this.

http://20.124.6.107:80

This has a public static IP and is installed using an authenticated Azure user. Security groups are set to only allow port 80 and 22.


------------------------------------------------------------------------------------------------------------------------------------------------

For step three, the Kubernetes Kompose application was used to convert the Docker Compose file to Kubernetes deployment manifests.
The Compose file 'docker-compose-kube.yml' is used for this.

ie `kompose convert -f docker-compose-kube.yml` 

Client and container images were push to Docker hub to make deployment easier.
The application works as it should when the all the manifests are deployed to minikube.
You can see the app using a browser at the ip address assigned by minikube for the client service.

`minikube get services`


