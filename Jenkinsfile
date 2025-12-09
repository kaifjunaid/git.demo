pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        APP_DIR = '/var/www/nextjs-app'
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Cloning latest repository...'
                git(url: 'https://github.com/kaifjunaid/git.demo.git', branch: 'main')
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    #!/bin/bash
                    set -e

                    echo "===== DEBUG: Node & NPM version ====="
                    node -v || echo "ERROR: Node not installed"
                    npm -v || echo "ERROR: NPM not installed"

                    echo "Preparing deployment directory..."
                    sudo mkdir -p ${APP_DIR}
                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    echo "Syncing latest repository..."
                    rsync -av --delete --exclude ".git" --exclude "node_modules" ./ ${APP_DIR}/

                    cd ${APP_DIR}

                    echo "===== DEBUG: Running npm install ====="
                    npm install --verbose --no-audit --no-fund 2>&1 | tee npm-debug.log

                    echo "===== DEBUG: Running Next.js build ====="
                    npm run build 2>&1 | tee build-debug.log

                    echo "Installing PM2 globally..."
                    sudo npm install -g pm2 || true

                    echo "Stopping previous app..."
                    pm2 delete nextjs-app || true

                    echo "Starting new app with PM2..."
                    pm2 start "npm start" --name nextjs-app
                    pm2 save
                '''
            }
        }
    }
}
