pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '837868235243'
        AWS_DEFAULT_REGION = 'ap-south-1'
        IMAGE_REPO_NAME = 'image-regi'
        IMAGE_TAG = 'latest'
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github-cred', url: 'https://github.com/shrusti-acharya/cloud-cicd-lab.git'
            }
        }

        stage('Logging in to Amazon ECR') {
            steps {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
            }
        }

        stage('Building Image') {
            steps {
                sh "docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
            }
        }

        stage('Pushing to ECR') {
            steps {
                sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG}"
            }
        }

        stage('Deploying Container') {
            steps {
                sh "docker stop flask-app || true"
                sh "docker rm flask-app || true"
                sh "docker run -d -p 5000:5000 --name flask-app ${REPOSITORY_URI}:${IMAGE_TAG}"
            }
        }
    }
}
