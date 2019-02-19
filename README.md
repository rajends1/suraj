# suraj
DevOps_Jenkins_Graddle_python_terraform_puppet_helm_AWS_Azure

CI/CD Flow:

1.Jenkins setup on AWS
2.Integrate Artifactory with Jenkins
3.Configure Sonarqube in jenkins [sonar.sources is the main property for static code analysis. With this property, you inform SonarQube which directory needs to be analyzed]
4.Record Jacoco Coverage report
5.Configure Jenkins with  spring boot project using Gradle/Maven
6.JaCoCo to the Gradle configuration.

The process goes as follows:

The developer pushes a code change to GitHub.
Jenkins detects the change, triggers the build, and checks out the current code.
Jenkins executes the commit phase and builds the Docker image.
Jenkins pushes the image to Docker registry.
Jenkins runs the Docker container in the staging environment.
Staging the Docker host needs to pull the image from the Docker registry.
Jenkins runs the acceptance test suite against the application running in the staging environment.

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
