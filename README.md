AWS CloudFormation and Automated S3 Website Deployment

1. Project Overview

The objective of this project was to create an Amazon S3 static website using AWS CloudFormation and automate the deployment of an existing index.html file from GitHub. GitHub Actions and OpenID Connect (OIDC) were used to authenticate with AWS and upload the website file without requiring a manual upload.

2. Implementation Steps

Step 1: Create the CloudFormation Template

A CloudFormation template named CF-S3-Template.yaml was created. It defines:

An Amazon S3 bucket.

Static website hosting with index.html as the index document.

A bucket policy that permits public read access to website objects.

Outputs for the bucket name and website URL.

Retention policies to help preserve the bucket when the stack is deleted or replaced.

The template creates the website infrastructure but does not upload the HTML file.

Step 2: Configure CloudFormation Git Sync

A deployment configuration file, CF-S3-deploy.yaml, was added to the repository. It references the CloudFormation template and allows AWS CloudFormation Git sync to use the repository configuration.

The stack was deployed in the eu-north-1 AWS Region.

Step 3: Configure GitHub and AWS Authentication

An IAM role named GithubActions was configured to use GitHub's OIDC identity provider. This allows GitHub Actions to request temporary AWS credentials without storing long-term AWS access keys in GitHub.

The trust policy was updated to support the repository's immutable subject claim format and permit the relevant main and s3-template branches.

An IAM permissions policy was also configured to allow the workflow to upload the website file to the target S3 bucket.

Step 4: Create the GitHub Actions Workflow

A workflow named deploy-website.yml was created under .github/workflows/.

The workflow performs these operations:

Checks out the repository.

Configures AWS credentials using OIDC.

Uploads index.html to the S3 bucket using the AWS CLI.

Sets the uploaded object's content type to text/html.

The workflow is triggered by pushes to the s3-template branch and can also be started manually.

Step 5: Troubleshoot OIDC Authentication

The first workflow execution failed with an sts:AssumeRoleWithWebIdentity authorization error.

The IAM trust policy initially used the traditional GitHub subject claim format. The repository was configured to use immutable subject claims, so the trust policy was updated to match the repository and owner identifiers displayed in GitHub's OIDC settings.

After this change, the workflow successfully authenticated with AWS and uploaded index.html.

Step 6: Test the Deployment and Use Pull Requests

The uploaded website was accessed through its S3 website endpoint, confirming that the deployment worked.

The s3-template branch was used for development and testing. Pull Requests were used to manage changes intended for the main branch.

3. Result

The project demonstrated an automated website deployment workflow connecting GitHub, GitHub Actions, AWS IAM, Amazon S3 and CloudFormation.

The HTML file was uploaded successfully without manual file transfer, and the website was accessible through the S3 website endpoint.

4. AI Assistance and Prompt Documentation

AI assistance was used to understand the CloudFormation template, plan the deployment workflow, configure the relevant AWS and GitHub components, and troubleshoot authentication errors. The implementation was tested by running the workflow and accessing the deployed website.

The following prompts are consolidated versions of the main requests made during development, rather than a verbatim transcript of every conversation message.

Prompt 1: Understanding CloudFormation

"Explain the structure of the official AWS CloudFormation template for S3 static website hosting. Explain the purpose of each section without rewriting the template, and ask questions to check my understanding."

Prompt 2: Automating Website Uploads

"I have an existing index.html file in my GitHub repository and want to make it publicly accessible through Amazon S3 static website hosting. I do not want to upload the file manually. Explain the possible automation approaches."

Prompt 3: Configuring GitHub Actions and AWS

"Guide me through configuring GitHub Actions to deploy index.html to an Amazon S3 bucket. Use AWS OIDC authentication instead of long-term access keys, explain the required IAM trust and permissions policies, and help me configure the workflow for my development branch."

Prompt 4: Troubleshooting OIDC Authentication

"Help me troubleshoot the GitHub Actions error 'Not authorized to perform sts'. Explain how to compare the GitHub OIDC subject claim with the AWS IAM role trust policy and identify the required changes."

Prompt 5: Verifying Deployment and Costs

"Help me verify that GitHub Actions successfully uploads index.html to S3 and that the website is accessible. Explain which AWS resources and services may incur charges and how to clean up resources safely."

AI Tool and Model

Tool: ChatGPT

Model: GPT-6

Purpose: Technical explanation, implementation guidance, troubleshooting and verification planning.

Technical Documentation Consulted

AWS CloudFormation documentation: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html

Amazon S3 static website hosting: https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html

GitHub Actions documentation: https://docs.github.com/en/actions

GitHub Actions OIDC with AWS: https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws

5. Conclusion

The project successfully demonstrated infrastructure provisioning and automated static website deployment. CloudFormation defined the S3 website infrastructure, while GitHub Actions and OIDC provided the deployment mechanism. Testing confirmed that the website file could be uploaded automatically and accessed through the website endpoint.