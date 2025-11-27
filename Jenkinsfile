pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'dockerhub_username/my-web-app'
    DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
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
        echo "Running tests with Maven..."
        bat '"C:\\Program Files\\apache-maven-3.9.11\\bin\\mvn" test'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_IMAGE}:latest")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
            dockerImage.push('latest')
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
}
