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

### docker push asia-south1-docker.pkg.dev/${PROJECT_ID}/yolo-client-repo/yolo-client-app:v1