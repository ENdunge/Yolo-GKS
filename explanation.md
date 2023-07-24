## Setting project ID environment variable to google cloud project ID.
This variable is used when building the container image and pus it to the repository

#### export PROJECT_ID=PROJECT_ID
#### echo $PROJECT_ID
### gcloud config set project $PROJECT_ID

## Setting Project Region
In the project directory, you can run:
set default region to asia-south1. This is the nearest region and it was selected to minimize latency.

## Creating the project repostory

### gcloud artifacts repositories create yolo-client-repo --repository-format=docker --location=asia-south1 --description="Docker repository"

## Building the container image

### git clone https://github.com/ENdunge/Yolo-GKS.git
### cd Yolo-GKS/client
### docker build -t asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1 .
This command instructs Docker to build the image using the Dockerfile in the current directory, save it to the local environment, and tag it with the same name i.e asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1

The asia-south1-docker.pkg.dev prefix refers to Artifact Registry, regional host for the repository.

## Cheking Docker images
### docker images
Run the docker images command to verify that the build was successful.

## Add IAM policy bindings to the service account

### gcloud artifacts repositories add-iam-policy-binding yolo-client-repo --location=asia-south1 --member=serviceAccount:114506246157-compute@developer.gserviceaccount.com --role="roles/artifactregistry.reader"

## Running the container locally
To test the container image using docker locally, run:
### docker run --rm -p 8080:8080 asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1

## Pushing the Docker image to Artifact Registry
The container image must be uploaded to a registry so that GKE cluster can download and run the container image.

Configure the docker command-line tool to authenticate to Artifact Registry
### gcloud auth configure-docker asia-south1-docker.pkg.dev

Push the docker image to the repository
### docker push asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1

## Creating a GKE cluster
###  gcloud config set compute/region asia-south1
###  gcloud container clusters create-auto yolo-client-cluster

## Deploying the app to GKE

1. Ensure that GKE is connected
### gcloud container clusters get-credentials hello-cluster --region asia-south1

2. Create a Kubernetes Deployment for the yolo-client-app docker image
### kubectl create deployment yolo-client-app --image=asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1

3. Set the baseline number of deployments to 3
### kubectl scale deployment yolo-client-app --replicas=3

4. Create a HorizontalPodAutoscaler resource for the deployment
### kubectl autoscale deployment yolo-client-app --cpu-percent=80 --min=1 --max=5

5. To get pods
### kubectl get pods