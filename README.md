# Modern CICD on GCP

## 0. Activate APIs

```bash
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
### Export some useful variables
```bash
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
export PROJECT_NUMBER=$(gcloud projects list \
    --filter=${PROJECT_ID} --format="value(PROJECT_NUMBER)")
```

### Grant Cloud Build access to GKE
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/container.developer"
```
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/clouddeploy.releaser"
```
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/iam.serviceAccountUser"
```
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/ondemandscanning.admin"
```
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/run.admin"
```

## 5. Update your cluster url in `0-pipeline.yaml` for GKE and CR

## 6. Update GKE and CR yaml files to set your `$PROJECT_ID`

## 7. For Cloud Run demo, please follow the pre-requisite steps of [this doc](https://docs.google.com/document/d/1DFunajJsevYhoVg6x3xC8YInzGLkRTLk_TxfpY_esQk/edit#) to get a config controller cluster