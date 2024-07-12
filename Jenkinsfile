pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')  // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE = 'asadkhan08/fullstackproject:16-alpine-prod'  // Your Docker image name
        PRODUCTION_SERVER = 'localhost'  // Localhost address since Jenkins and Docker are on the same server
        PROD_DOCKER_IMAGE = 'asadkhan08/fullstackproject:16-alpine'  // Docker image for production deployment
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Step 1: Build Docker image
                    sh "docker build -t ${DOCKER_IMAGE} ./client/ "
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

                stage('Build and Push Docker Image') {
            steps {
                script {
                    //sh "docker build -t ${DOCKER_IMAGE} ./client/ "
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Production Server') {
            steps {
                script {
                    // Step 3: Pull and deploy Docker image on production server
                    // docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                    //     docker.image("${PROD_DOCKER_IMAGE}").pull()
                    sh "docker pull ${DOCKER_IMAGE}"
                    }
                    sh """
                    docker stop my_production_container || true
                    docker rm my_production_container || true
                    docker run -d --name my_production_container -p 80:80 ${PROD_DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Optional: Clean up Docker images after deployment
                    sh "docker rmi ${DOCKER_IMAGE} || true"
                    sh "docker rmi ${PROD_DOCKER_IMAGE} || true"
                }
            }
        }
    }
}
 
