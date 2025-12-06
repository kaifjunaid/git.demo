pipeline {
    agent any

    // Auto-run pipeline on every Git push
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

                    # Sync project files
                    rsync -av --delete \
                        --exclude='.git' \
                        --exclude='node_modules' \
                        ./ ${APP_DIR}/

                    cd ${APP_DIR}

                    sudo npm install
                    sudo npm run build

                    # Kill any existing process on port 3000
                    sudo fuser -k 3000/tcp || true

                   
                    npm run start
                """
            }
        }
    }
}
