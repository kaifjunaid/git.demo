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
                echo 'Cloning latest repository...'
                git(
                    url: 'https://github.com/kaifjunaid/git.demo.git',
                    branch: 'main'
                )
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying latest version to EC2...'
                sh """
                    # Prepare deployment directory
                    sudo mkdir -p ${APP_DIR}
                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    # Remove old node_modules and package-lock
                    rm -rf ${APP_DIR}/node_modules
                    rm -f ${APP_DIR}/package-lock.json

                    # Sync new files
                    rsync -av --delete --exclude='.git' --exclude='node_modules' ./ ${APP_DIR}/

                    # Install & build as Jenkins user
                    cd ${APP_DIR}
                    npm install --no-audit --no-fund
                    npm run build

                    # Stop old process
                    sudo fuser -k 3000/tcp || true

                    # Start app
                    npm run start &
                """
            }
        }
    }
}
