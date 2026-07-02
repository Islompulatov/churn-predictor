pipeline {
    agent any

    environment {
        APP_NAME = "fastapi-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source venv/bin/activate
                pytest
                '''
            }
        }

        stage('Start FastAPI Check') {
            steps {
                sh '''
                source venv/bin/activate
                uvicorn app:app --host 0.0.0.0 --port 8000 &
                sleep 5
                kill $!
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $APP_NAME .
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}