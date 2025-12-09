pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        APP_DIR = '/var/www/nextjs-app'
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Source') {
            steps {
                echo 'Checking out repository...'
                checkout scm
            }
        }

        stage('Deploy to EC2') {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    sh '''
                        #!/bin/bash
                        set -e

                        echo "===== Node & NPM Version ====="
                        node -v
                        npm -v

                        echo "===== Preparing Deployment Directory ====="
                        sudo mkdir -p ${APP_DIR}
                        sudo chown -R jenkins:jenkins ${APP_DIR}

                        echo "===== Syncing Code ====="
                        rsync -av --delete \
                          --exclude ".git" \
                          --exclude "node_modules" \
                          ./ ${APP_DIR}/

                        cd ${APP_DIR}

                        echo "===== Installing Dependencies (npm ci) ====="
                        npm ci --no-audit --no-fund

                        echo "===== Building Next.js App ====="
                        npm run build

                        echo "===== Ensuring PM2 Installed ====="
                        if ! command -v pm2 >/dev/null 2>&1; then
                          sudo npm install -g pm2
                        fi

                        echo "===== Restarting App ====="
                        pm2 delete nextjs-app || true
                        pm2 start npm --name "nextjs-app" -- start
                        pm2 save
                    '''
                }
            }
        } 
    }

    post {
        success {
            echo ' Deployment completed successfully'
        }
        failure {
            echo ' Deployment failed – check logs above'
        }
    }
}