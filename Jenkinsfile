pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME = credentials('DOCKER_HUB_USERNAME') // Jenkins credentials for Docker Hub Username
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_PASSWORD') // Jenkins credentials for Docker Hub Password
        DOCKER_IMAGE_NAME = 'my-pipeline:latest'  // Docker image name
        CONTAINER_NAME = 'my-pipeline-container'  // Docker container name
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clean up the workspace if it exists
                    bat '''
                    IF EXIST docker-image-jenkins-pipeline (
                        rmdir /s /q docker-image-jenkins-pipeline
                    )
                    '''
                }
                // Cloning the repository
                bat '''
                git clone https://github.com/shannonsequeira/docker-image-jenkins-pipeline.git
                cd docker-image-jenkins-pipeline
                git checkout main
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Building the Docker image
                    bat "docker build -t %DOCKER_IMAGE_NAME% ./docker-image-jenkins-pipeline"
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Stop and remove the existing container if it exists
                    bat '''
                    docker stop %CONTAINER_NAME% || exit 0
                    docker rm %CONTAINER_NAME% || exit 0
                    '''
                    // Running the Docker container in daemon mode
                    bat "docker run -d --name %CONTAINER_NAME% %DOCKER_IMAGE_NAME%"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    bat "echo %DOCKER_HUB_PASSWORD% | docker login -u %DOCKER_HUB_USERNAME% --password-stdin"
                    
                    // Tag the image with Docker Hub username before pushing
                    bat "docker tag %DOCKER_IMAGE_NAME% %DOCKER_HUB_USERNAME%/my-pipeline:latest"

                    // Push the Docker image to Docker Hub
                    bat "docker push %DOCKER_HUB_USERNAME%/my-pipeline:latest"
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
