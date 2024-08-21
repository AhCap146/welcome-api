pipeline {
    agent any
    environment {
        // Define environment variables
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE_NAME = 'welcome-api'
        DOCKER_TAG = "${BUILD_NUMBER}"
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
        stage('Deploy to Kubernetes'){
            steps{
                sh """
               sed -i 's|image: \\${DOCKER_IMAGE_NAME}:\\${DOCKER_TAG}|image: ${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}|g' webapi-deployment.yaml

               echo "Updated webapi-deployment.yaml:"
                           cat webapi-deployment.yaml
                kubectl apply -f webapi-deployment.yaml
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
