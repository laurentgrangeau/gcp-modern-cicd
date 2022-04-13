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
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
export PROJECT_NUMBER=$(gcloud projects list \
    --filter=${PROJECT_ID} --format="value(PROJECT_NUMBER)")
```

```sh
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/clouddeploy.releaser"
```
```sh
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/container.developer"
```
```sh
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/ondemandscanning.admin"
```
```sh
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/run.admin"
```


