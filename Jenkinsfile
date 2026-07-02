pipeline {
    agent any

    environment {
        EC2_IP = '13.235.114.96'
    }

    stages {

        stage('Clone Backend Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-creds',
                    url: 'https://github.com/cloudhostingky-alt/E-flow-Backend.git'
            }
        }

        stage('Clean Old Build') {
            steps {
                sh '''
                docker system prune -f || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build --no-cache -t eflow-backend-app:latest .
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                docker stop eflow-backend || true
                docker rm eflow-backend || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh '''
                docker run -d \
                    -p 3000:3000 \
                    --name eflow-backend \
                    --restart unless-stopped \
                    eflow-backend-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment successful. App running at http://${EC2_IP}:3000"
        }
        failure {
            echo "Build failed. Check the logs above."
        }
    }
}
