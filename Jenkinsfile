pipeline {
    agent any

    environment {
        // Your Docker Hub ID (lowercase is safer)
        DOCKER_USER = "ganavi02m"
        IMAGE_NAME = "flask-docker-integration"
        // This ID must match the one you created in Jenkins Credentials UI
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-auth-id')
    }

    stages {
        stage('Pull from GitHub') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                // This uses the Credentials ID to securely log in
                sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
                
                // Tagging the image with your username
                sh "docker tag ${IMAGE_NAME}:latest ${DOCKER_USER}/${IMAGE_NAME}:latest"
                
                // Pushing to the cloud
                sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
            }
        }

        stage('Verify by Pulling') {
            steps {
                // Remove the local copy first to prove the pull works
                sh "docker rmi ${DOCKER_USER}/${IMAGE_NAME}:latest || true"
                
                // Pull it back down from Docker Hub
                sh "docker pull ${DOCKER_USER}/${IMAGE_NAME}:latest"
                echo "Successfully pulled the image from the cloud!"
            }
        }
    }
}
