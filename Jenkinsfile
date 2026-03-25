pipeline {
    agent any

    environment {
        // Replace with your Docker Hub ID
        DOCKER_USER = "your_docker_id"
        IMAGE_NAME = "jenkins-docker-lab"
        // This ID MUST match the Credential ID you create in Jenkins UI
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-auth-id')
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls the latest code from your GitHub repo
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Builds the image using the Dockerfile in your repo
                    sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Logs into Docker Hub using the credentials stored in Jenkins
                    sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
                    // Pushes the image to your public repository
                    sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Pull and Verify') {
            steps {
                script {
                    // Clean up any old containers if they exist
                    sh "docker rm -f test-container || true"
                    // Pull the fresh image back from Docker Hub
                    sh "docker pull ${DOCKER_USER}/${IMAGE_NAME}:latest"
                    // Run it locally to ensure it actually works
                    sh "docker run -d --name test-container -p 5000:5000 ${DOCKER_USER}/${IMAGE_NAME}:latest"
                    // Wait a moment and check if the app responds
                    sh "sleep 5 && curl http://localhost:5000"
                    // Stop the test container after verification
                    sh "docker stop test-container && docker rm test-container"
                }
            }
        }
    }

    post {
        success {
            echo "Successfully pushed and verified the image!"
        }
        failure {
            echo "Build failed. Check the Jenkins Console Output for errors."
        }
    }
}
