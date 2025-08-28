pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t calculator-app .'
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'python:3.9'
                }
            }
            steps {
                echo 'Running tests...'
                sh 'pip install -r requirements.txt'
                sh 'pytest tests'
            }
        }

        stage('Push to ECR') {
            when {
                changeRequest()
            }
            steps {
                echo 'Push image to ECR with PR tag here'
            }
        }
    }
}
