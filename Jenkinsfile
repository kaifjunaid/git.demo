pipeline {
    agent any

    triggers {
        githubPush()
    }

    options {
        // Better logs + safety
        ansiColor('xterm')
        timeout(time: 40, unit: 'MINUTES')
        skipDefaultCheckout(true)
    }

    environment {
        APP_DIR  = '/var/www/nextjs-app'
        NODE_ENV = 'production'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out latest code...'
                deleteDir() // clean workspace
                git branch: 'main', url: 'https://github.com/kaifjunaid/git.demo.git'
            }
        }

        stage('Install dependencies & Build') {
            steps {
                sh '''
                    #!/bin/bash
                    set -euxo pipefail

                    echo "===== DEBUG: Node & NPM version ====="
                    node -v
                    npm -v

                    echo "===== Installing dependencies (npm ci) ====="
                    # Faster and more reliable for CI than npm install
                    npm ci --no-audit --no-fund

                    echo "===== Building Next.js app ====="
                    npm run build
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    #!/bin/bash
                    set -euxo pipefail

                    echo "Preparing deployment directory..."
                    sudo mkdir -p "$APP_DIR"
                    sudo chown -R jenkins:jenkins "$APP_DIR"

                    echo "Syncing latest build to $APP_DIR ..."
                    rsync -av --delete \
                        --exclude ".git" \
                        --exclude "node_modules" \
                        ./ "$APP_DIR/"

                    cd "$APP_DIR"

                    echo "Checking PM2..."
                    if ! command -v pm2 >/dev/null 2>&1; then
                      echo "PM2 not found, installing globally..."
                      sudo npm install -g pm2
                    fi

                    echo "Restarting app with PM2..."
                    pm2 delete nextjs-app || true
                    pm2 start "npm start" --name nextjs-app
                    pm2 save || true

                    echo "Deployment finished successfully."
                '''
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed – check npm-debug logs or Jenkins logs for details."
        }
        always {
            // Helpful if npm fails
            sh '''
              echo "Listing debug logs if present..."
              ls -l npm-debug.log build-debug.log 2>/dev/null || true
            '''
        }
    }
}