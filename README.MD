# Deploying Docker Applications to Cloud Run with CI/CD

## Overview

This tutorial provides a step-by-step guide to setting up a CI/CD pipeline for automated Docker deployment to Google Cloud Run. By leveraging Google Cloud Build and GitHub Actions, we will automate the process of building, testing, and deploying containerized applications to Cloud Run. This ensures a seamless and efficient deployment workflow.

## Prerequisites

Before starting, ensure you have the following:

- A **Google Cloud Platform (GCP)** account with billing enabled
- The **gcloud CLI** installed and configured
- A **Dockerfile** in your project repository

## What You'll Learn

- How to containerize an application with Docker
- How to push Docker images to Google Container Registry (GCR) or Artifact Registry
- How to Cloud Build for continuous deployment
- How to deploy and manage applications on Cloud Run

Let's get started! 🚀

## Step 1: Create a New Repository in Google Cloud Artifact Registry

To store your Docker images in Google Cloud Artifact Registry, follow these steps:

1. Navigate to the **Google Cloud Console** and go to **Artifact Registry**.
2. Click **Create Repository**.
3. In the **Create Repository** section:
   - Choose **Docker** as the format.
   - Select **Standard** as the mode.
   - Set the **Location Type** based on your needs (either **Region** or **Multi-region**).
4. To enable vulnerability scanning for your images, enable **Artifact Analysis**.
5. Click the **Create** button to finalize the repository setup.

With your repository set up, you are now ready to push your Docker images to Google Cloud Artifact Registry!

Step 2: Create a Cloud Build Trigger
------------------------------------

1.  Navigate to **Cloud Build** in the Google Cloud Console.
    
2.  Select the **Triggers** tab and click **Add Trigger**.
    
3.  In the **Create Trigger** section:
    
    *   Provide a name for your trigger.
        
    *   Leave all other settings as default.
        
    *   Under **Repository**, select your version control system (e.g., GitHub).
        
4.  Ensure your code is pushed to the repository before proceeding.
    
5.  Connect your version control account to Cloud Build.
    
6.  Select the branch you plan to deploy.
    
7.  Click **Create** to finalize the setup.


Step 3: Place and modify this cloudbuild.yaml file in your repository
------------------------------------

```
steps:
  # Build the Docker image using Buildx for the amd64 architecture
  - name: 'gcr.io/cloud-builders/docker'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        docker buildx create --use
        docker buildx build --platform linux/amd64 --load -t "Your artifact registry URL:$SHORT_SHA" .

  # Push the image to Google Artifact Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'Your artifact registry URL:$SHORT_SHA']

  # Deploy the image to Cloud Run
  - name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
    entrypoint: 'gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'macromonitor'  # Replace with your Cloud Run service name that you want to deploy
      - '--image="Your artifact registry URL:$SHORT_SHA"'
      - '--region="your region"'  # Replace with your region
      - '--platform=managed'
      - '--allow-unauthenticated'

substitutions:
  _SHORT_SHA: 'latest'

images:
  - "Your artifact registry URL:$SHORT_SHA"
```

Step 4: Try to modify your local repository then pushed it to your GitHub repository to trigger the Cloud Build
------------------------------------
1. You can visit your cloud build section to see the build process and the deployment process.
2. Upon successful deployment, you can access your application on Cloud Run using the provided URL. 

Congratulations! You have successfully set up a CI/CD pipeline for deploying Docker applications to Google Cloud Run.


## Authors

- [@harmoniousmoss](https://www.github.com/harmoniousmoss)

If you have any questions or need further assistance, feel free to reach out at syaefulbahri[at]protonmail.com.

