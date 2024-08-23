pipeline {
    agent any
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

//         stage('Build') {
//             steps {
//                 // Run Maven to build the project
//                sh './mvnw -B -DskipTests clean package'
//             }
//         }
//
//         stage('Test') {
//             steps {
//                 // Run tests with Maven
//                 sh './mvnw test'
//             }
//         }
//
//         stage('Build Docker Image') {
//             steps {
//                 script {
//                     // Build the Docker image
//                     sh """
//                     docker build -t ${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .
//                     """
//                 }
//             }
//         }
//         stage('Login to Dockerhub'){
//             steps {
//
//                 sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
//             }
//         }
//         stage('Push Image to Dockerhub'){
//             steps{
//
//                  sh """
//                 docker push ${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}
//                 """
//             }
//         }
        stage('Deploy to Kubernetes'){
            steps{
                sh """
                kubectl apply -f webapi-deployment.yaml -n jenkins
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
