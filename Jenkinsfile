pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "992382545251.dkr.ecr.us-east-1.amazonaws.com/yuvalz-calculator-app"
    }

    stages {
        stage('Build') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t calculator-app .'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh '''
                    docker run --rm calculator-app bash -c "
                        pip install pytest &&
                        PYTHONPATH=/app pytest tests
                    "
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                        docker tag calculator-app:latest $ECR_REPO:latest
                        docker push $ECR_REPO:latest
                    '''
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                sshagent(credentials: ['ec2-user']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@54.174.89.229 "
                          aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO &&
                          docker pull $ECR_REPO:latest &&
                          docker stop calculator || true &&
                          docker rm calculator || true &&
                          docker run -d --name calculator -p 80:5000 $ECR_REPO:latest
                        "
                    '''
                }
            }
        }
    }
}
