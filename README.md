# Modern CICD on GCP

## 0. Activate APIs

```bash
gcloud services enable \
  artifactregistry.googleapis.com \
  binaryauthorization.googleapis.com \
  cloudbuild.googleapis.com \
  clouddeploy.googleapis.com \
  cloudkms.googleapis.com \
  container.googleapis.com \
  containeranalysis.googleapis.com \
  containerfilesystem.googleapis.com \
  containerregistry.googleapis.com \
  krmapihosting.googleapis.com \
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

### Grant Cloud Build access to Cloud Run
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/run.developer"
```

### Grant Cloud Build permission to act as Cloud Run runtime service account
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/iam.serviceAccountUser"
```

### Grant Cloud Build permission to perform a vulnerability scan
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/ondemandscanning.admin"
```

### Grant Cloud Build permission to create Cloud Deploy releases
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
        --member="serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
        --role="roles/clouddeploy.releaser"
```

### Grant Config Controller permission to manage GCP
```bash
export SA_EMAIL="$(kubectl get ConfigConnectorContext -n config-control \
    -o jsonpath='{.items[0].spec.googleServiceAccount}' 2> /dev/null)"
gcloud projects add-iam-policy-binding "${PROJECT_ID}" \
    --member "serviceAccount:${SA_EMAIL}" \
    --role "roles/owner" \
    --project "${PROJECT_ID}"
```

## 5. Update your cluster url in `0-pipeline.yaml` for GKE and CR

## 6. Update GKE and CR yaml files to set your `$PROJECT_ID`

## 7. For Cloud Run demo, please follow the pre-requisite steps of [this doc](https://docs.google.com/document/d/1DFunajJsevYhoVg6x3xC8YInzGLkRTLk_TxfpY_esQk/edit#) to get a config controller cluster

## 8. Enable Binary Authorization on Cloud Run
### One-time build for binauthz-attestation
git clone https://github.com/GoogleCloudPlatform/cloud-builders-community.git
cd cloud-builders-community/binauthz-attestation
gcloud builds submit . --config cloudbuild.yaml
gcloud container binauthz policy import binauth-policy/policy.yaml
### Create KMS key for binauthz-attestation
gcloud kms keyrings create binauthz-attestors \
  --location europe-west1
gcloud kms keys create binauthz-decrypt-key \
  --keyring binauthz-attestors \
  --location europe-west1 \
  --purpose "asymmetric-encryption" \
  --default-algorithm "rsa-decrypt-oaep-2048-sha256"
gcloud kms keys create binauthz-signing-key \
  --keyring binauthz-attestors \
  --location europe-west1 \
  --purpose "asymmetric-signing" \
  --default-algorithm "rsa-sign-pkcs1-2048-sha256"
### Grant Cloud Build permission to view the created attestor
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
  --member "serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
  --role "roles/binaryauthorization.attestorsViewer"
```
### Grant Cloud Build permission to verify the KMS cryptokeys
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
  --member "serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
  --role "roles/cloudkms.signerVerifier"
```
### Grant Cloud Build permission to attach notes
```bash
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
  --member "serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com" \
  --role "roles/containeranalysis.notes.attacher"
```
### Enable Binary Authorization for Cloud Run
gcloud run services update SERVICE_NAME --binary-authorization=default