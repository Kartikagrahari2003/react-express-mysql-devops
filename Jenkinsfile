pipeline {
    agent any

    stages {

        stage('Checkout Verification') {
            steps {
                echo 'Repository cloned successfully'

                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Environment Verification') {
            steps {
                sh 'git --version'
                sh 'docker --version'
                sh 'docker compose version'
            }
        }

        stage('Debug User') {
            steps {
                sh '''
                    echo "===== WHOAMI ====="
                    whoami

                    echo "===== ID ====="
                    id

                    echo "===== GROUPS ====="
                    groups

                    echo "===== DOCKER SOCKET ====="
                    ls -l /var/run/docker.sock

                    echo "===== DOCKER TEST ====="
                    docker ps
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images...'
                sh 'docker compose build'
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Stopping old containers...'

                sh '''
                    docker compose down || true
                '''

                echo 'Starting application...'

                sh '''
                    docker compose up -d
                '''
            }
        }

    }
}