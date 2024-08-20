pipeline {
    agent any

    tools {
        maven 'maven-3.9.2'
    }
    environment {
        // Define environment variables
        DOCKER_IMAGE_NAME = 'welcome-api'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Run Maven to build the project
                sh './mvnw clean package'
            }
        }

        stage('Test') {
            steps {
                // Run tests with Maven
                sh './mvnw test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh """
                    docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .
                    """
                }
            }
        }
    }

    post {
        always {
            // Archive the artifacts for later use
            archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
        }
    }
}
