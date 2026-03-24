pipeline {
    agent any

    environment {
        DOCKER_USER = "monishab17"
        // This must match the ID you created in Jenkins Credentials
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
                // We use withCredentials to safely get your Docker Hub username and password
                withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER_CRED')]) {
                    echo '📤 Logging in and Pushing Image to Docker Hub...'
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER_CRED --password-stdin"
                    sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy Local') {
            steps {
                echo '🚀 Refreshing Local Container...'
                // Stop and remove container if it exists, then run the new one
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
