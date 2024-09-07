pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'your-docker-hub-credentials-id'
        DOCKER_IMAGE_NAME = 'my-pipeline:latest'
        CONTAINER_NAME = 'my-pipeline-container'
    }
    stages {
        stage('Checkout') {
            steps {
                // Cloning the repository
                bat 'git clone https://github.com/shannonsequeira/docker-image-jenkins-pipeline.git'
                bat 'cd docker-image-jenkins-pipeline && git checkout main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Building the Docker image
                    bat "docker build -t ${DOCKER_IMAGE_NAME} ."
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Stop and remove existing Docker container if it exists
                    bat 'docker stop %CONTAINER_NAME% || exit 0'
                    bat 'docker rm %CONTAINER_NAME% || exit 0'
                    // Running the Docker container in daemon mode
                    bat "docker run -d --name ${CONTAINER_NAME} ${DOCKER_IMAGE_NAME}"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS_ID}", usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        bat "echo %DOCKER_HUB_PASSWORD% | docker login -u %DOCKER_HUB_USERNAME% --password-stdin"
                    }
                    // Push the Docker image to Docker Hub
                    bat "docker push ${DOCKER_IMAGE_NAME}"
                }
            }
        }
    }
    post {
        always {
            bat 'echo Pipeline execution completed.'
        }
    }
}
