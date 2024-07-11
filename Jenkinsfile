pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')  // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE = 'asadkhan08/fullstackproject:16-alpine'  // Your Docker image name
        PRODUCTION_SERVER = 'localhost'  // Localhost address since Jenkins and Docker are on the same server
        PROD_DOCKER_IMAGE = 'asadkhan08/fullstackproject:16-alpine'  // Docker image for production deployment
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Step 1: Build Docker image
                    sh "docker build -t ${DOCKER_IMAGE} -f Dockerfile.test ."
                }
            }
        }

        stage('Push to Docker Hub') {
          
            steps {
                script {
                    // Step 2: Push Docker image to Docker Hub
                    docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Deploy to Production Server') {
            steps {
                script {
                    // Step 3: Pull and deploy Docker image on production server
                    docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                        docker.image("${PROD_DOCKER_IMAGE}").pull()
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
    
