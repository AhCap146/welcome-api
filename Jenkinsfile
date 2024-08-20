pipeline {
    agent any

    tools {
        maven 'maven-3.9.2'
    }

    environment {
        // Define environment variables
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
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
        stage('Fix Permissions') {
             steps {
                // Ensure mvnw script is executable
                sh 'chmod +x mvnw'
              }
        }

        stage('Build') {
            steps {
                // Run Maven to build the project
               sh './mvnw -B -DskipTests clean package'
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
                    docker build -t ${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .
                    """
                }
            }
        }
        stage('login to dockerhub'){
            steps {

                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push image to docker hub'){
            steps{

                 sh """
                docker push ${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}
                """
            }
        }

    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
