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
                    args '-u root:root'
                }
            }
            steps {
                echo 'Running tests...'
                sh 'pip install -r requirements.txt'
                sh 'pip install pytest'
                sh 'export PYTHONPATH=$PYTHONPATH:$(pwd) && pytest tests'
            }
        }

        stage('Push to ECR') {
            when {
                changeRequest()
            }
            steps {
                script {
                    def accountId = "992382545251"
                    def region = "us-east-1"
                    def repoName = "yuvalz-calculator-app"
                    def imageTag = "pr-${env.CHANGE_ID}-${env.BUILD_NUMBER}"
                    def ecrImage = "${accountId}.dkr.ecr.${region}.amazonaws.com/${repoName}:${imageTag}"

                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                        sh """
                          aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${accountId}.dkr.ecr.${region}.amazonaws.com
                          docker tag calculator-app:latest ${ecrImage}
                          docker push ${ecrImage}
                        """
                    }
                    echo "✅ Pushed image to ECR: ${ecrImage}"
                }
            }
        }
