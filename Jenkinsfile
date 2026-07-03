pipeline {
    agent any

    environment {
        AWS_REGION = "us-west-2"
        ECR_REPO = "devops-repo"
        ACCOUNT_ID = "288757602829"
        IMAGE = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
        CLUSTER_NAME = "churn-cluster"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPO:latest .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin \
                $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $ECR_REPO:latest $IMAGE'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $IMAGE'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME

                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml

                kubectl rollout status deployment/churn-app
                '''
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment successful!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}