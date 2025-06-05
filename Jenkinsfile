pipeline {
    agent any

    environment {
        IMAGE_NAME = 'selvakumar2209/devops-node-app'
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/Selvakumar2209/Nodejs.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds') {
                    script {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to K3s') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}
