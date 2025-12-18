pipeline {
    agent any

    options {
        durabilityHint('MAX_SURVIVABILITY')
        timestamps()
    }

    environment {
        APP_DIR = '/var/www/nextjs-app'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                echo 'Cleaning Jenkins Workspace...'
                deleteDir()
            }
        }

        stage('Clone Repo') {
            steps {
                echo 'Cloning the repo...'
                git branch: 'main',
                    url: 'https://github.com/kaifjunaid/git.demo.git'
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying to EC2...'
                sh '''
                    sudo mkdir -p $APP_DIR
                    sudo chown -R jenkins:jenkins $APP_DIR

                    rsync -av --delete \
                        --exclude='.git' \
                        --exclude='node_modules' \
                        ./ $APP_DIR

                    cd $APP_DIR

                    rm -rf node_modules
                    npm ci --no-audit --no-fund

                    npm run build

                    sudo fuser -k 3000/tcp || true

                    nohup npm run start > app.log 2>&1 &
                '''
            }
        }
    }
}
