# Deploying a Portfolio Website to AWS S3 using GitHub Actions

## About This Project
 
  This project is a practice exercise to learn website deployment using AWS S3 and GitHub Actions.

The website used in this project is forked from the TWS Portfolio Repository. By using a pre-built portfolio template, the focus was on understanding and implementing:

  - Point one Hosting static websites on AWS S3.
  - Point two Automating deployments with GitHub Actions.
  - Point three Configuring AWS S3 bucket policies for public access.

## Prerequisites

Before you begin, ensure that you have the following:
- An **AWS account** with access to **S3** and **IAM** services.
- A **GitHub** account and repository where you want to deploy the website.
- AWS **Access Key ID** and **Secret Access Key** for setting up GitHub secrets.
- Basic knowledge of AWS S3 and GitHub Actions.

## Steps to Set Up

### Step 1: Create an S3 Bucket

1. Go to the [AWS S3 Console](https://s3.console.aws.amazon.com/s3/).
2. Click **Create bucket**.
3. Choose a globally unique name for your bucket (e.g., `my-portfolio-website`).
4. Select the AWS region where you want to host your website.
5. Under **Bucket settings for Block Public Access**, uncheck the option **Block all public access**. (You will configure public access with an S3 bucket policy later.)
6. Click **Create bucket**.

### Step 2: Configure S3 for Static Website Hosting

1. In the S3 Console, select the bucket you just created.
2. Go to the **Properties** tab.
3. Scroll down to the **Static website hosting** section.
4. Enable **Static website hosting**.
5. Set the **Index document** to `index.html` (or the name of your homepage file).
6. Optionally, set the **Error document** to `error.html` (optional).
7. Click **Save changes**.

### Step 3: Create and Attach an S3 Bucket Policy

To allow public access to the files in your S3 bucket, follow these steps to create an appropriate policy:

1. In the **Permissions** tab of your S3 bucket, go to **Bucket Policy**.
2. Add the following policy to allow public read access:

```json
{
    "Version": "2012-10-17",
    "Id": "Policy1735328265352",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-portfolio-website/*"
        }
    ]
}
```
### Step 4: Add the GitHub Actions Workflow

Create a GitHub Actions workflow file to automate the deployment process:

1. Create a directory `.github/workflows/` in the root of your repository.
2. Inside the `workflows` folder, create a file named `build-and-deploy.yml`.
3. Add the following content to the file:

```yaml
name: Build and Deploy to AWS S3

on:
  push:
    branches:
      - main  # Trigger the action on push to the main branch

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up AWS CLI
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1  # Replace with your AWS region

    - name: Sync website files to S3
      run: |
        aws s3 sync ./ s3://my-portfolio-website --delete --exact-timestamps
```
### Step 5: Set Up AWS Credentials in GitHub Secrets
To allow GitHub Actions to access your AWS account, you need to set up AWS credentials as GitHub secrets:

Go to your GitHub repository and click on Settings.
Under Secrets and Variables, select Actions.
Add the following secrets:

`AWS_ACCESS_KEY_ID`: Your AWS Access Key ID.

`AWS_SECRET_ACCESS_KEY`: Your AWS Secret Access Key.

### Step 6: Push Changes to Trigger Deployment
Commit and push your changes (including the GitHub Actions workflow file) to the main branch.
GitHub Actions will automatically trigger and deploy your website to the S3 bucket.
### Step 7: Access Your Website
Once the deployment is complete, you can access your website via the S3 bucket's static website:

## Live Website

You can view the deployed website here: ([http://tws-junoon-bucket.s3-website-us-east-1.amazonaws.com](http://tws-junoon-bucket.s3-website.ap-south-1.amazonaws.com/))


