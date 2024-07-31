# Step-by-Step Process to Build a Nimbus-File-Exchange Platform

# Project Description:
Nimbus File Exchange is a serverless file sharing platform built on AWS, allowing users to securely upload and download files via a simple HTTP API. It leverages AWS Lambda for serverless compute, API Gateway for RESTful API management, and Amazon S3 for durable and scalable object storage.

# Project Architecture
![image](https://github.com/user-attachments/assets/c11133fe-fe41-4959-b2d5-7f92be9d31ef)

## Prerequisite

Before you can deploy the Nimbus File Exchange, ensure that you have the following prerequisite:

- An AWS account with appropriate permissions to create Lambda functions, API Gateway, and S3 buckets.

## Deployment Steps

Follow these steps to deploy this project on your AWS account:

1. **Create an S3 Bucket**: Create an S3 bucket to store the uploaded files. For example, you can create a bucket named `my-file-bucket`.

2. **Create the Upload Lambda Function**: Create a Lambda function named `FunctionForUploads` with the following configuration:
   - Runtime: Python 3.9
   - Execution Role: An IAM role with permissions to write to the S3 bucket created in step 1.
   - Code: Use the provided `FunctionForUploads` Python code.

3. **Create the Download Lambda Function**: Create a Lambda function named `FunctionForDownloads` with the following configuration:
   - Runtime: Python 3.9
   - Execution Role: An IAM role with permissions to read from the S3 bucket created in step 1.
   - Code: Use the provided `FunctionForDownloads` Python code.

4. **Create an API Gateway**: Create an API Gateway named `my-file-api` with the following resources and methods:
   - Resource: `/files`
     - Method: `POST` (integrated with the `FunctionForUploads` Lambda)
     - Method: `GET` (integrated with the `FunctionForDownloads` Lambda)

5. **Configure the GET Method**:
   - In the API Gateway, navigate to the `GET` method for the `/files` resource.
   - Edit the method request and enable "Validate Query String Parameters and Headers."
   - Set the request body content type to `text/plain`.
   - Edit the integration request and add a mapping template for `application/json` content type with the following body:
     ```json
     {
       "queryStringParameters": {
         "fileName": "$input.params('fileName')"
       }
     }
     ```

6. **Configure the POST Method**:
   - In the API Gateway, navigate to the `POST` method for the `/files` resource.
   - Edit the integration request and add a mapping template for `text/plain` content type with the following body:
     ```json
     {
       "body": "$input.body",
       "queryStringParameters": {
         "fileName": "$input.params('fileName')"
       }
     }
     ```

7. **Deploy the API Gateway**:
   - In the API Gateway, navigate to the "Actions" menu and select "Deploy API."
   - Choose a deployment stage (e.g., `dev`) and deploy the API.

8. **Testing**:
   - **Upload a File**:
     - You can use the `curl` utility:
       ```
       curl --location 'https://<API-ID>.execute-api.<REGION>.amazonaws.com/dev/files?fileName=<file-name>.txt \
       --header 'Content-Type: text/plain' \
       --data 'text here'
       ```
   - **Download a File**:
     - You can use the `curl` utility:
       ```
       curl --location 'https://<API-ID>.execute-api.<REGION>.amazonaws.com/dev/files?fileName=<file-name>.txt'
       ```

Congratulations! You have successfully deployed the Nimbus File Exchange on your AWS account.
