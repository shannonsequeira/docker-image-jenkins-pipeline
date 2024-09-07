pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME = credentials('DOCKER_HUB_USERNAME') // Jenkins credential ID for Docker Hub username
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_PASSWORD') // Jenkins credential ID for Docker Hub password
        DOCKER_IMAGE_NAME = 'my-pipeline:latest'
        CONTAINER_NAME = 'my-pipeline-container'
    }
    stages {
        stage('Checkout') {
            steps {
                bat '''git clone https://github.com/shannonsequeira/docker-image-jenkins-pipeline.git
                git checkout main
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %DOCKER_IMAGE_NAME% .'
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    bat '''
                    docker stop %CONTAINER_NAME% || exit 0
                    docker rm %CONTAINER_NAME% || exit 0
                    '''
                    bat 'docker run -d --name %CONTAINER_NAME% %DOCKER_IMAGE_NAME%'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Secure Docker Hub login
                    bat '''
                    echo %DOCKER_HUB_PASSWORD% | docker login -u %DOCKER_HUB_USERNAME% --password-stdin
                    '''
                    bat 'docker push %DOCKER_IMAGE_NAME%'
                }
            }
        }
    }
    post {
        always {
            bat 'echo \'Pipeline execution completed.\''
        }
    }
}
