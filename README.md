# suraj
DevOps_Jenkins_Graddle_python_terraform_puppet_helm_AWS_Azure

CI/CD Flow:

CI:
CI  with Jenkins,SOnar,gradle,jacakoo and Docker

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

CD: 
Continuous Deployment to Lambda function using Bitbucket pipeline

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

Continuous Deployment to Amazon S3 using Bitbucket pipeline:

Docker image ( We are using docker customized image which has all the software which is required to run the application)
We are using caches feature of bitbucket pipeline. We are doing custom caching for node_modules folder
Automate version using npm version patch of our nodejs application and committing the package.json automatically using pipeline only. To do the auto git commit set the SSH public key setup. Generate the public key of your repository and copy that public key to your account SSH keys.
Run the npm install command
After build is successful, store the artifacts to S3 bucket with versions
For deployment to production — make the trigger as manual.
Prerequisite:

S3 bucket on AWS
bitbucket-pipeline.yaml file
AWS Secret Keys
Environment Variables

How to setup a Continuous Deployment to Amazon S3 using Bitbucket pipeline
--Bitbucket Pipelines is an integrated CI/CD service, built into Bitbucket. It allows you to automatically build, test and even deploy your code, based on a configuration file in your repository. ... The bitbucket-pipelines.yml file holds all the build configurations for your repository.

Create a bitbucket-pipeline.yaml file at the root of your project
For the specific bitbucket repository -> Go to Settings -> Add Repository variables
AWS_SECRET_ACCESS_KEY
AWS_ACCESS_KEY_ID
AWS_REGION
All the above variables are required to connect to s3 bucket from bitbucket pipeline.

3. We are setting up a Continuous Deployment to Amazon S3 using Bitbucket pipeline for nodejs application

# This is a sample build configuration for JavaScript.
# Check our guides at https://confluence.atlassian.com/x/14UWN for more examples.
# Only use spaces to indent your .yml configuration.
# -----
# You can specify a custom docker image from Docker Hub as your build environment.
image:  inventory_base
pipelines:
branches:
master:
- step:
caches:
- node-admin
- node-web
name: Build and Generate Artifacts
script:
- git config --global user.email "test"
- git config --global user.name "build machine"
- node -v
- ruby -v
- VERSION=$(npm version patch  -m "auto verison tick [skip CI]")
- VERSION=$(echo $VERSION | cut -c 2-)
- echo $VERSION
- npm install
- bower install --allow-root
- git add package.json
- grunt build
- aws s3 sync dist s3://m-test-files/admin/$VERSION
- aws s3 cp app/scripts/version.js s3://m-test-files/admin/$VERSION/scripts/version.js
- git commit -m "[skip CI]"
- git push
- step:
caches:


Jenkins Pipeline example:

It is used to create stages/pipeline of all the steps which are required for building the project.

How to initialize software and set path

stage(‘Terraform Init’) {
 steps {
 script {
 def tfHome = tool name: ‘Terraform’
 def anHome = tool name: ‘Ansible’
 env.PATH = “${anHome}:${env.PATH}”
 env.PATH = “${tfHome}:${env.PATH}”
 }
 sh ‘terraform — version’
 sh ‘ansible — version’
 
 }
 }

How to declare variable

def my_list

pipeline {
 agent any
 stages {
 stage(‘stage1’) {
 steps {
 script {
 my_list = “nidhi”
 }
 }
 }
 
 stage(‘stage2’) {
 steps {
 echo “$my_list”
 
 }
 }
 }
}

Second Example
def test=”nik”

pipeline {
 agent any
 stages {
 
 
 
 stage(‘stage2’) {
 steps {
 echo “$test”
 
 }
 }
 }
}

Example of pipeline which will use variables/checkout/dockerpush

def source=”/var/jenkins_home/workspace/mvv-web-pipeline1/MVVWeb/build/libs/.jar”
def destination=”MVVWeb/docker/”
def DTR=”dtest”
def DOCKER_NAME=”mvv/mvv-web”
pipeline {
 agent any
 stages {
 stage(“Checkout”) {
 steps {
git url: ‘ssh://git@bitbucket.sandata.com:7999/ohio/automation_scripts.git’
 }
 }
 stage(“Compile”) {
 steps {
 dir (‘MVVWeb’) {
 sh “./gradlew build”
 }
}
}
stage(“Copy”) {
 steps {
 
sh “cp /var/jenkins_home/workspace/mvv-web-pipeline1/MVVWeb/build/libs/*.jar /var/jenkins_home/workspace/mvv-web-pipeline1/MVVWeb/docker”
 }
 
} 
stage(“Docker build”) {
 steps {
 dir(‘MVVWeb/docker’)
 {
 sh “docker build -t ${DTR}/${DOCKER_NAME}:v$BUILD_ID .”
 }
 }
 }

stage(“Docker push”) {
 steps {
 sh “docker login ${DTR} — username=peter — password=mypass”
 sh “docker push ${DTR}/${DOCKER_NAME}:v$BUILD_ID”

}
 
}
 }
 }

// build with parameters
pipeline {
    agent any
    parameters {
        choice(
            // choices are a string of newline separated values
            // https://issues.jenkins-ci.org/browse/JENKINS-41180
            choices: 'create\nupdate',
            description: '',
            name: 'REQUESTED_ACTION')
            
             choice(
            // choices are a string of newline separated values
            // https://issues.jenkins-ci.org/browse/JENKINS-41180
            choices: '100\n50',
            description: '',
            name: 'percentage')
    }
stages {
        stage ('Create') {
            when {
                // Only say hello if a "greeting" is requested
                expression { params.REQUESTED_ACTION == 'create' }
            }
            steps {
                echo "Hello, create!"
            }
        }
        
        stage ('Percentage') {
            when {
                // Only say hello if a "greeting" is requested
                expression { params.percentage == '100' }
            }
            steps {
                echo "Hello, 100!"
            }
        }
    }
}
drop down in jenkinspipeline

pipeline {
    agent none
stages {
        stage ('First') {
            agent any
            steps {
                echo "First dummy stage"
            }
        }
        stage ('Input') {
          
            steps {
                script {
                    myStage = input message: 'What service type do you want to run now?', parameters: [choice(choices: 'Create\nUpdate', description: '', name: 'Stage')]
                }
                echo myStage
            }
        }
stage('Stage1') {
            when {
                expression { myStage == 'Create' }
            }
            steps {
                echo "Running Service: Create"
            }
        }
stage('Stage2') {
            when {
                expression { myStage == 'Update' }
            }
            steps {
                echo "Running Service:Update"
            }
        }
}
}
def userInput = input(
 id: ‘userInput’, message: ‘Let\’s promote?’, parameters: [
 [$class: ‘TextParameterDefinition’, defaultValue: ‘uat’, description: ‘Environment’, name: ‘env’],
 [$class: ‘TextParameterDefinition’, defaultValue: ‘uat1’, description: ‘Target’, name: ‘target’]
])
pipeline {
 agent any
 stages {
 
 stage (‘promotion’)
 {
steps{
echo (“Env: “+userInput[‘env’])
echo (“Target: “+userInput[‘target’])
 
}
 }
 }
}
- node-admin
- node-web
name: Deploy to staging
deployment: staging
trigger: manual
script:
- cd deploy
- ./deploy_staging.sh
definitions:     
 caches:          
node-admin: admin/node_modules          
node-web: web/node_modules


Manage Ansible Playbook for multiple environments
------------------

Now in your main.yml file call the roles as mentioned below:

---
- hosts: all
  become: yes
  gather_facts: no
  vars_files:
    - environments/{{ env }}//group_vars//main.yml
  roles:
    # -  ssh 
     
- hosts: sandbox
  become: yes
  gather_facts: no
  vars_files:
     - environments/{{ env }}//group_vars//main.yml
  roles:
      -  web
      -  php
- hosts: proxy
  become: yes
  gather_facts: no
  vars_files:
     - environments/{{ env }}//group_vars//main.yml
  roles:
      -  certbot
      
- hosts: proxy
  become: yes
  gather_facts: no
  vars_files:
     - environments/{{ env }}//group_vars//main.yml
  roles:
      -  nginx
Run your ansible playbook

ansible-playbook main.yml -i environments/dev/inventory/hosts — extra-vars env=dev
