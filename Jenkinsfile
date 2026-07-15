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

    }
}