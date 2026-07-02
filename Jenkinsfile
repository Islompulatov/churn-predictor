pipeline {
    agent any

    environment {
        APP_NAME = "fastapi-app"
        PATH = "/usr/local/bin:${env.PATH}"
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
                PID=$!

                sleep 5

                kill $PID || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Docker path:"
                which docker || true
                docker --version

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