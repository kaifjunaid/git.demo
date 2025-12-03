pipeline {
    agent any

    tools {
        nodejs "node18"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                echo "Cleaning workspace"
                deleteDir()
            }
        }

        stage('Clone Repo') {
            steps {
                git 'https://github.com/kaifjunaid/git.demo.git'
            }
        }

        stage('Build on Jenkins') {
            steps {
                sh """
                    npm config set fetch-timeout 600000
                    npm install
                """
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo "Deploying to EC2..."
            }
        }
    }
}   
