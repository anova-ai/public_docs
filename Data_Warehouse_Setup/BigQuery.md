# BigQuery Data Connection Documentation

This file provides instructions on how to create a service account in Google BigQuery and what information to send to your Anova rep so we can create your data connection.

# Prerequisites

Before getting started, please make sure:

1. You have a valid Google Cloud account and an active project
2. You have permissions to:
   - Create service accounts (Service Account Admin)
   - Assign IAM roles (IAM Role Admin)

# Create Service Account

## Step 1: Choose Your Google Cloud Project

1. Sign in to your Google Cloud account: https://console.cloud.google.com
2. In the top navigation bar, open the project selector
3. Select your project

## Step 2: Enable the BigQuery API

1. Open the BigQuery API link: https://console.cloud.google.com/apis/library/bigquery.googleapis.com
2. If not already enabled, click `Enable`

## Step 3: Create a Service Account and Assign Permissions

1. Open the menu in the top left
2. Go to **IAM & Admin > Service Accounts**
3. Click `+ Create service account`
4. Enter the following:
   - Service account name: **anova-bigquery-access**
   - Service account ID (optional, generated automatically)
   - Service account description (optional)
5. Click `Create and continue`
6. In **Select a role**, select `BigQuery Job User` (you can type the role to easily filter)
7. Click `+ Add another role`
8. In **Select a role**, select `BigQuery Data Viewer` (you can type the role to easily filter)
9. Click `Continue` (you don't need to do anything else in the **Permissions** section)
10. Click `Done` (you don't need to do anything in the **Principals with access** section)

## Step 4: Generate a Service Account Key

1. After the service account is created, click on the name of the service account to open its details
2. Click the `Keys` tab
3. Click `Add key` and then `Create new key`
4. Select `JSON` as the **Key type** and click `Create`
5. A JSON file should automatically download to your computer, keep this **safe and secure** as you won't be able to download it again

# Send Details to Anova

Once you've completed the above, reach out to your Anova rep and they will outline what is needed and how to securely send the information so that we can create the connection, generate an initial semantic layer, and start the training process.

# Support

If you have any questions or need any help as you do the above, feel free to reach out to your Anova rep or email <support@anova.ai>.