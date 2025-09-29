pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "flask-example-cicd"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
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
            pytest test --disable-warnings -q
          "
        '''
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          docker rm -f flask-example || true
          docker run -d --name flask-example -p 5000:5000 $DOCKER_IMAGE:$BUILD_NUMBER
        '''
      }
    }
  }
}
