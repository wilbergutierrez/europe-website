# Website Deployment on Google Cloud

This README provides a summary of the key steps for deploying a website on Google Cloud Platform using Google Kubernetes Engine (GKE) and Google Compute Engine.

## Prerequisites

Before starting, ensure you have the following tools installed and configured:

  * **Google Cloud CLI (gcloud):** The command-line interface for Google Cloud.
  * **Kubectl:** The command-line tool for interacting with Kubernetes clusters.
  * **A Google Cloud project** with billing enabled.

## Deployment Process Overview

The deployment process generally involves three main phases: setting up your Google Cloud environment, deploying the website's components, and configuring access.

### 1\. Google Cloud Environment Setup

This section covers the initial setup of your Google Cloud project and services.

1.  **Authenticate with Google Cloud:**
    ```bash
    gcloud auth login
    ```
2.  **Set the active project:**
    ```bash
    gcloud config set project **YOUR_PROJECT_ID**
    ```
3.  **Enable necessary APIs:**
    ```bash
    gcloud services enable container.googleapis.com
    gcloud services enable compute.googleapis.com
    ```

### 2\. Google Kubernetes Engine (GKE) Deployment

This is the core of your website deployment, where you run your application in a managed Kubernetes cluster.

1.  **Create a GKE cluster:**
    ```bash
    gcloud container clusters create **YOUR_CLUSTER_NAME** --zone ***YOUR_COMPUTE_ZONE***
    ```
2.  **Get cluster credentials:** This command configures `kubectl` to use your new cluster.
    ```bash
    gcloud container clusters get-credentials **YOUR_CLUSTER_NAME** --zone **YOUR_COMPUTE_ZONE**
    ```
3.  **Build and push the Docker image of your website:**
    ```bash
    gcloud builds submit --tag gcr.io/ **YOUR_PROJECT_ID** / **YOUR_IMAGE_NAME** : **YOUR_IMAGE_TAG**
    ```
4.  **Deploy your application to GKE:** This command creates a Kubernetes deployment using your Docker image.
    ```bash
    kubectl create deployment **YOUR_DEPLOYMENT_NAME** --image=gcr.io/ **YOUR_PROJECT_ID** / **YOUR_IMAGE_NAME** : **YOUR_IMAGE_TAG**
    ```
5.  **Expose your application:** This creates a load balancer to expose your website to the internet.
    ```bash
    kubectl expose deployment **YOUR_DEPLOYMENT_NAME** --type=LoadBalancer --port=80 --target-port=8080
    ```
    *Note: The target port should match the port your application is listening on.*

### 3\. Google Compute Engine (Deployment)

This guide will walk you through the entire process, from cloning your GitHub repository to making your website accessible on the web.

### 1\. Create a Google Compute Engine Instance

First, you need to create a virtual machine (VM) to host your website.

1.  Navigate to the Google Cloud Console and select your project.
2.  In the left-hand navigation menu, go to **Compute Engine** \> **VM instances**.
3.  Click **CREATE INSTANCE**.
4.  Give your instance a name (e.g., `europe-travel-website`).
5.  Choose a **Region** and **Zone** that are geographically close to your users to reduce latency.
6.  Under **Boot disk**, you can leave the default settings (e.g., Debian).
7.  Under **Firewall**, check the boxes for **Allow HTTP traffic** and **Allow HTTPS traffic**. This is essential to allow web traffic to reach your VM.
8.  Click **CREATE**.

### 2\. Connect to Your VM and Install a Web Server

Once your VM is running, you'll need to connect to it and install a web server to serve your website files. Apache is a great, easy-to-use option.

1.  In the **VM instances** list, find your newly created instance.
2.  In the **Connect** column, click the **SSH** button. This will open a terminal window in your browser.
3.  In the terminal, update the package list:
    ```bash
    sudo apt-get update
    ```
4.  Install the Apache web server:
    ```bash
    sudo apt-get install apache2 -y
    ```
5.  Verify that Apache is running:
    ```bash
    sudo systemctl status apache2
    ```
    If successful, you should see `active (running)`.

### 3\. Clone the GitHub Repository and Copy Files

Now, you'll get your website files from GitHub and place them in the correct directory.

1.  Install Git on your VM:
    ```bash
    sudo apt-get install git -y
    ```
2.  Clone your repository to a temporary location. **Make sure you use the HTTPS URL to avoid authentication issues.**
    ```bash
    git clone https://github.com/GNiruthian/Europe-Travel-Website-html-css-js.git
    ```
    This will create a new directory named `Europe-Travel-Website-html-css-js`.
3.  Copy the website files from the cloned directory to Apache's default web directory (`/var/www/html/`).
    ```bash
    sudo cp -r Europe-Travel-Website-html-css-js/* /var/www/html/
    ```
4.  Apache may have a default `index.html` file. You should remove it to ensure your website's home page is displayed.
    ```bash
    sudo rm /var/www/html/index.html
    ```

### 4\. Access Your Website

Your website is now live\! You can access it using the external IP address of your VM.

1.  Return to the **VM instances** page in the Google Cloud Console.
2.  Locate your VM's **External IP** address in the table.
3.  Open a new browser tab and paste the IP address into the address bar.

You should now see your "Europe Travel Website" live and accessible to the public.

