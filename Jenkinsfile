pipeline {
    agent any

    environment {
        IMAGE_NAME = 'selvakumar2209/devops-node-app'
    }

    // Define a variable outside stages but inside pipeline
    // Unfortunately, declarative pipeline does not allow variable declarations outside stages,
    // so use `script` block or store in `currentBuild` object

    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Selvakumar2209/Nodejs.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                   dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                   // Save tag for later stages in env for convenience
                   env.IMAGE_TAG = "${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // use the dockerImage variable directly instead of docker.image(...)
                    withDockerRegistry(credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Update Deployment File') {
           steps {
              script {
                 sh "sed -i 's|<build_number>|${BUILD_NUMBER}|' k8s/deployment.yaml"
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
