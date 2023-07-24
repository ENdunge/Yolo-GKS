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