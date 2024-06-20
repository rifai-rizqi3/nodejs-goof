pipeline {
    agent any

    environment {
        DOCKER_COMPOSE = 'docker-compose'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub repository
                git 'https://github.com/rifai-rizqi3/nodejs-goof.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the Docker images
                    sh "${DOCKER_COMPOSE} build"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Start the Docker containers
                    sh "${DOCKER_COMPOSE} up -d"
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up Docker containers
                sh "${DOCKER_COMPOSE} down"
            }
        }
    }
}
