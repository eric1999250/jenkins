pipeline {
    agent any

    environment {
        // Docker configuration
        DOCKER_IMAGE = 'ericuwineza/my-web-app'  // FIXED
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // Must match Jenkins credentials

        // Maven configuration
        MAVEN_HOME = "C:\\Program Files\\apache-maven-3.9.11"
        PATH = "${env.MAVEN_HOME}\\bin;${env.PATH}"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
                echo "Workspace cleaned"
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
                echo "SCM checkout done"
            }
        }

        stage('Build') {
            steps {
                echo "Building the project with Maven..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" clean install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:latest")
                    echo "Docker image built: ${DOCKER_IMAGE}:latest"
                }
            }
        }

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

        stage('Deploy') {
            steps {
                echo "Deployment complete!"
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
