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

        stage('Build Information') {
            steps {
                sh '''
                echo "Build Number : $BUILD_NUMBER"
                echo "Build URL    : $BUILD_URL"
                echo "Branch       : $BRANCH_NAME"
                echo "Workspace    : $WORKSPACE"
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
                sh 'docker compose down || true'

                echo 'Starting application...'
                sh 'docker compose up -d'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'docker ps'
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                curl --fail http://localhost:3000
                '''
            }
        }

        stage('Application Logs') {
            steps {
                sh 'docker compose logs --tail=20'
            }
        }

        stage('Docker Cleanup') {
            steps {
                sh '''
                docker image prune -f
                docker container prune -f
                '''
            }
        }

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
    }

    post {
        always {
            echo "Pipeline Finished"
        }

        success {
            echo "Application deployed successfully."
        }

        failure {
            echo "Deployment failed. Check console logs."
        }
    }
}