pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ericuwineza/my-web-app'
        DOCKER_TAG = 'latest'
        APP_PORT = '8080'
        CONTAINER_PORT = '3000'
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

        stage('Build Maven Project') {
            steps {
                echo "Building project with Maven..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" clean install'
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running tests..."
                bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    echo "Docker image built: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage('Push Docker Image to Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                     usernameVariable: 'DOCKER_USER',
                                                     passwordVariable: 'DOCKER_PASS')]) {
                        bat """
                        docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker logout
                        """
                        echo "Docker image pushed to Docker Hub"
                    }
                }
            }
        }

        stage('Deploy to Local Docker Host') {
            steps {
                echo "Deploying container on local Docker host..."
                bat """
                docker rm -f my-web-app || exit 0
                docker run -d --name my-web-app -p ${APP_PORT}:${CONTAINER_PORT} --restart unless-stopped ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }

        stage('Verify Local Deployment') {
            steps {
                echo "Verifying local deployment..."
                bat """
                timeout /t 5
                docker ps | findstr my-web-app
                docker logs my-web-app
                curl -f http://localhost:${APP_PORT} || exit 1
                """
            }
        }

        stage('Deploy to Remote Docker Host') {
            steps {
                sshagent(['remote-host-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no user@remote-host "docker pull ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    ssh user@remote-host "docker rm -f my-web-app || true"
                    ssh user@remote-host "docker run -d --name my-web-app -p ${APP_PORT}:${CONTAINER_PORT} --restart unless-stopped ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    """
                }
            }
        }

        stage('Verify Remote Deployment') {
            steps {
                sshagent(['remote-host-ssh-key']) {
                    sh """
                    ssh user@remote-host "docker ps | grep my-web-app"
                    ssh user@remote-host "curl -f http://localhost:${APP_PORT} || exit 1"
                    """
                }
            }
        }

    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
            echo "Local app running at http://localhost:${APP_PORT}"
        }
        failure {
            echo "❌ Pipeline failed! Check logs above."
            bat 'docker logs my-web-app || exit 0'
        }
    }
}
