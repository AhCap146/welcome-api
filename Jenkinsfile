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

        stage('Update YAML and Deploy to Kubernetes') {
            steps {
                  script {
                            sh """
                            echo "Updating image in webapi-deployment.yaml using yq"
                             yq e '.spec.template.spec.containers[0].image = "${DOCKERHUB_CREDENTIALS_USR}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"' -i webapi-deployment.yaml

                            # Display the updated YAML file for verification
                            echo "Updated webapi-deployment.yaml:"
                            cat webapi-deployment.yaml

                            # Apply the deployment to Kubernetes
                            kubectl apply -f webapi-deployment.yaml
                            """
                        }
                    }
                }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
