pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-example-cicd:latest .'
            }
        }

        stage('Run Unit Tests (Pytest)') {
            steps {
                sh '''
                  docker run --rm -v "$PWD":/code -w /code flask-example-cicd:latest \
                  sh -c "pip install pytest && pytest /code/test --disable-warnings -q"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                  docker rm -f flask-example || true
                  docker run -d --name flask-example -p 5000:8080 flask-example-cicd:latest
                '''
            }
        }
    }
}
