Run the following command to create a new service account:

    gcloud iam service-accounts create github-actions-sa \
      --description="Service Account for GitHub Actions" \
      --display-name="GitHub Actions SA"

This creates a service account named github-actions-sa.
Step 4: Grant Required IAM Roles

Assign the required permissions for your workflows. Example roles for typical CI/CD tasks:

    gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
      --member="serviceAccount:github-actions-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
      --role="roles/storage.admin"
    
    gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
      --member="serviceAccount:github-actions-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
      --role="roles/viewer"
    
    gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
      --member="serviceAccount:github-actions-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
      --role="roles/editor"

Modify roles as needed, depending on the permissions required for your GitHub Actions workflow.
2. Create and Download a JSON Key

Once the service account is created and assigned roles, generate a key for it:

    gcloud iam service-accounts keys create service-account-key.json \
      --iam-account=github-actions-sa@YOUR_PROJECT_ID.iam.gserviceaccount.com

This will download the service-account-key.json file to your local machine.
