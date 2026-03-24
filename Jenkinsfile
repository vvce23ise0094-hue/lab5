pipeline {
    agent any

    environment {
        // 1. Updated to your exact Docker Hub username
        DOCKER_USER = "monishab17"
        
        // 2. This must match the ID you created in Jenkins Credentials
        REGISTRY_CREDENTIALS = 'lab5'
        
        IMAGE_NAME = "my_meg_app"
        CONTAINER_NAME = "my_meg_container"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📥 Pulling code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building Docker Image...'
                // Tags the image correctly for your account
                sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running Python Syntax Check...'
                sh "python3 -m py_compile app.py"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // This uses the 'docker-hub-creds' from your Jenkins Credentials vault
                    docker.withRegistry('', "${REGISTRY_CREDENTIALS}") {
                        echo '📤 Pushing Image to Docker Hub...'
                        sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Deploy Local') {
            steps {
                echo '🚀 Refreshing Local Container...'
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
                sh "docker run -d -p 5000:5000 --name ${CONTAINER_NAME} ${DOCKER_USER}/${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        success {
            echo '✅ Success! Your image is on Docker Hub and running locally.'
        }
        failure {
            echo '❌ Pipeline Failed. Check the Console Output for login or naming errors.'
        }
    }
}
