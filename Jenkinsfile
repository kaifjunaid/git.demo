pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        APP_DIR = '/var/www/nextjs-app'
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
                sh """
                    sudo mkdir -p ${APP_DIR}
                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    rsync -av --delete \
                        --exclude='.git' \
                        --exclude='node_modules' \
                        ./ ${APP_DIR}/

                    cd ${APP_DIR}

                    # FIX: avoid Jenkins timeout issues
                    

                    sudo fuser -k 3000sudo chown -R jenkins:jenkins /var/www/nextjs-app
                    cd /var/www/nextjs-app
                    npm install --no-audit --no-fund
                    npm run build
                    /tcp || true

                    npm run start  
                """
            }
        }
    }
}


