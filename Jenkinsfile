pipeline {
    agent any

    environment {
        // Docker configuration
        DOCKER_IMAGE = 'dockerhub_username/my-web-app' // replace with your Docker Hub username
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // Jenkins credentials ID

        // Maven configuration
        MAVEN_HOME = "C:\\Program Files\\apache-maven-3.9.11"
        PATH = "${env.MAVEN_HOME}\\bin;${env.PATH}"
    }

    stages {

        // Stage 0: Clean workspace
        stage('Clean Workspace') {
            steps {
                deleteDir()
                echo "Workspace cleaned"
            }
        }

        // Stage 1: Checkout code from GitHub
        stage('Checkout') {
            steps {
                checkout scm
                echo "SCM checkout done"
            }
        }

        // Stage 2: Build with Maven
        stage('Build') {
            steps {
                echo "Building the project with Maven..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" clean install'
            }
        }

        // Stage 3: Run tests
        stage('Test') {
            steps {
                echo "Running tests..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" test'
            }
        }

        // Stage 4: Build Docker image
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:latest")
                    echo "Docker image built: ${DOCKER_IMAGE}:latest"
                }
            }
        }

        // Stage 5: Push Docker image to Docker Hub
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push('latest')
                        echo "Docker image pushed to Docker Hub"
                    }
                }
            }
        }

        // Stage 6: Deployment (optional)
        stage('Deploy') {
            steps {
                echo "Deployment complete!"
                // You can add actual deployment steps here if needed
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully ✅"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
