Go to Cloud Build
Connect repository
Create kubernetes cluster
Create Artifact Repo
Create Config Controller
gcloud anthos config controller create cc-deployrun --location europe-west1
Create trigger
gcloud beta builds triggers import 
Create Cloud Deploy pipeline
gcloud deploy apply --file 0-pipeline.yaml --region europe-west1
gcloud deploy delivery-pipelines describe run-pipeline --region europe-west1
Create Cloud Deploy target
gcloud deploy apply --region europe-west1 --file 0-target-cloudrun-dev.yaml
gcloud deploy apply --region europe-west1 --file 0-target-cloudrun-staging.yaml
gcloud deploy apply --region europe-west1 --file 0-target-cloudrun-prod.yaml