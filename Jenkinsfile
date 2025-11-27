pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'dockerhub_username/my-web-app' // replace with your Docker Hub username
    DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // Jenkins credentials ID
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        echo "Building the project..."
        bat 'mvn clean install' // replace with your build command if needed
      }
    }

    stage('Test') {
      steps {
        echo "Running tests..."
        bat 'mvn test' // replace with your test command if needed
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
        echo "Deployment complete!" // optional, can copy files to a folder/server
      }
    }
  }
}

