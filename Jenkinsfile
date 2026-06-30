pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'mca065/python_app:latest'
    DOCKER_USER = "${env.DOCKER_USER ?: 'mca065'}"
    DOCKER_PASS = "${env.DOCKER_PASS ?: 'Pavan@0097'}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Test') {
      steps {
        sh 'docker run --rm -v "$PWD:/app" -w /app python:3.11-slim sh -c "pip install -r requirements.txt && pytest -q"'
      }
    }

    stage('Build Docker image') {
      steps {
        sh "docker build -t ${DOCKER_IMAGE} ."
      }
    }

    stage('Push to Docker Hub') {
      steps {
        sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
        sh "docker push ${DOCKER_IMAGE}"
      }
    }

    stage('Deploy to Docker Swarm') {
      steps {
        sh 'docker stack deploy --compose-file docker-stack.yml python_app_stack'
      }
    }
  }
}
