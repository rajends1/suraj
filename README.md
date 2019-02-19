# suraj
DevOps_Jenkins_Graddle_python_terraform_puppet_helm_AWS_Azure

CI/CD Flow:

1.Docker image
2.We are using caches feature of bitbucket pipeline. We are doing custom caching for node_modules folder
3.Run the npm install command
4.Creating a zip file
5. Upload zip file to S3 bucket
6. Update the lambda function that will take the new artifacts from S3 buckets

Prerequisite:

1.S3 bucket on AWS
2.Lambda function
3.IAM Role
4.bitbucket-pipeline.yaml file
5.AWS Secret Keys
6. Environment Variables
