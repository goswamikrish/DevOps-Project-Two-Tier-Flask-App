pipeline {
    agent any
    stages {
        stage('Build image') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }
        stage('Deploy with docker compose') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }
}
