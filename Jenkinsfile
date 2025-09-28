pipeline {
  agent any
  environment {
    DOCKER_IMAGE = "flask-example-cicd"
    SONAR_HOST_URL = "http://localhost:9000"
    SONAR_PROJECT_KEY = "flask-example"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
      }
    }
    stage('Run Unit Tests (Pytest)') {
      steps {
        sh '''
          docker run --rm -v "$PWD":/app -w /app python:3.9-alpine sh -c "
            pip install -r requirements.txt &&
            pip install -e . &&
            pip install pytest &&
            pytest -q --disable-warnings --maxfail=1
          "
        '''
      }
    }
    stage('Code Quality - SonarQube') {
      environment { SONAR_TOKEN = credentials('sonar-token') }
      steps {
        sh '''
          docker run --rm \
            -e SONAR_HOST_URL=$SONAR_HOST_URL \
            -e SONAR_LOGIN=$SONAR_TOKEN \
            -v "$PWD":/usr/src sonarsource/sonar-scanner-cli \
            -Dsonar.projectKey=$SONAR_PROJECT_KEY \
            -Dsonar.sources=flaskr \
            -Dsonar.tests=test \
            -Dsonar.python.version=3.9 \
            -Dsonar.sourceEncoding=UTF-8
        '''
      }
    }
    stage('Deploy') {
      steps {
        sh '''
          docker rm -f flask-example || true
          docker run -d --name flask-example -p 8080:8080 $DOCKER_IMAGE:$BUILD_NUMBER
        '''
      }
    }
  }
}

