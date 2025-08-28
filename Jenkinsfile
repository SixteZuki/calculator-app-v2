pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:3.9'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
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
