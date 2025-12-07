pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
    APP_DIR = '/var/lib/jenkins/workspace/nextjs-app-deploy'
    }


    stages {

        stage('Clean Workspace') {
            steps {
                echo 'Cleaning Jenkins workspace...'
                deleteDir()
            }
        }

        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                git(
                    branch: 'main',
                    url: 'https://github.com/kaifjunaid/git.demo.git'
                )
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying application to EC2...'

                // Ensure app directory exists
                sh "sudo mkdir -p ${APP_DIR}"
                sh "sudo chown -R jenkins:jenkins ${APP_DIR}"

                // Sync project files
                sh "rsync -av --delete --exclude='.git' --exclude='node_modules' ./ ${APP_DIR}/"

                // Move into app directory
                dir("${APP_DIR}") {

                    // Kill existing app on port 3000
                    sh "sudo fuser -k 3000/tcp || true"

                    // Install dependencies safely
                    sh "npm install --no-audit --no-fund --silent"

                    // Build Next.js app
                    sh "npm run build"

                    // Stop any previously running app
                    sh "pkill -f 'npm run start' || true"

                    // Start app in background safely
                    sh "nohup npm run start > app.log 2>&1 &"
                }
            }
        }
    }
}
