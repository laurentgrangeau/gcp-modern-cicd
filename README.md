# Simple CICD demo on GCP

## 0. Activate APIs

```sh
gcloud services enable artifactregistry.googleapis.com \
binaryauthorization.googleapis.com \
cloudbuild.googleapis.com \
clouddeploy.googleapis.com \
container.googleapis.com \
containeranalysis.googleapis.com \
containerfilesystem.googleapis.com \
containerregistry.googleapis.com \
ondemandscanning.googleapis.com \
orgpolicy.googleapis.com \
oslogin.googleapis.com \
pubsub.googleapis.com \
run.googleapis.com \
sourcerepo.googleapis.com \
storage-api.googleapis.com \
storage-component.googleapis.com \
storage.googleapis.com
```

## 1. Create a Cloud Source Repository in your project

## 2. Create a repository in Artifact Registry

## 3. Create a Cloud Build Trigger (see triggers folder for example)

## 4. Add IAM roles to Cloud Build SA

```sh
gcloud projects add-iam-policy-binding PROJECT_ID \
        --member="service-PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/clouddeploy.releaser"
```
```sh
gcloud projects add-iam-policy-binding PROJECT_ID \
        --member="service-PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/container.developer"
```
```sh
gcloud projects add-iam-policy-binding PROJECT_ID \
        --member="service-PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/ondemandscanning.admin"
```
```sh
gcloud projects add-iam-policy-binding PROJECT_ID \
        --member="service-PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/run.admin"
```


