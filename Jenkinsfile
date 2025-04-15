pipeline {
    agent any

    environment {
        IMAGE_NAME = 'webapicontainer'
        ACR_NAME = 'acrregistryaryaninreact'
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        RESOURCE_GROUP = 'rg-docker-in-react'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/aryanjindal455/WebApiContainer.git'
            }
        }

        stage('Build .NET Project') {
            steps {
                sh 'dotnet restore'
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $ACR_LOGIN_SERVER/$IMAGE_NAME:latest ."
            }
        }

        stage('Login to ACR') {
            steps {
                sh "az acr login --name $ACR_NAME"
            }
        }

        stage('Push Docker Image to ACR') {
            steps {
                sh "docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:latest"
            }
        }

        stage('Terraform Init & Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform validate'
                    sh 'terraform apply -auto-approve'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
