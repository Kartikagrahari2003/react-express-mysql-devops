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

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region ap-south-1 | \
                docker login --username AWS --password-stdin \
                889088857850.dkr.ecr.ap-south-1.amazonaws.com
                '''
            }
        }

        stage('Tag Docker Images') {
            steps {
                sh '''
                docker tag react-express-mysql-devops-backend:latest \
                889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-backend:$BUILD_NUMBER

                docker tag react-express-mysql-devops-backend:latest \
                889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-backend:latest

                docker tag react-express-mysql-devops-frontend:latest \
                889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-frontend:$BUILD_NUMBER

                docker tag react-express-mysql-devops-frontend:latest \
                889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-frontend:latest
                '''
            }
        }

        stage('Push Images to Amazon ECR') {
            steps {
                sh '''
                docker push 889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-backend:$BUILD_NUMBER
                docker push 889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-backend:latest

                docker push 889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-frontend:$BUILD_NUMBER
                docker push 889088857850.dkr.ecr.ap-south-1.amazonaws.com/react-express-frontend:latest
                '''
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
                for i in {1..10}; do
                if curl --fail http://localhost:3000; then
                    exit 0
                fi
                echo "Waiting..."
                sleep 5
                done

                exit 1
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
    stage('Deploy to Production via SSM') {
        steps {
            sh '''
            aws ssm send-command \
            --instance-ids i-0f27a796006cc2e8e \
            --document-name "AWS-RunShellScript" \
            --region ap-south-1 \
            --parameters 'commands=[
            "cd /home/ubuntu/react-express-mysql-devops",
            "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 889088857850.dkr.ecr.ap-south-1.amazonaws.com",
            "docker compose -f compose.prod.yaml pull",
            "docker compose -f compose.prod.yaml up -d --remove-orphans"
            ]'
            '''
        }
    }
}