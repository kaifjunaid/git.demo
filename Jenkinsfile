pipeline {
    agent any

    environment {
        APP_DIR = "/var/www/nextjs-app"
        NODE_VERSION = "20.19.6"
        NVM_DIR = "$HOME/.nvm"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the repository...'
                checkout scm
            }
        }

        stage('Clean Workspace') {
            steps {
                echo 'Cleaning Jenkins workspace...'
                deleteDir()
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying latest version to EC2...'
                sh """
                    # Prepare app directory
                    mkdir -p ${APP_DIR}
                    chown -R jenkins:jenkins ${APP_DIR}

                    # Clean old build files
                    rm -rf ${APP_DIR}/node_modules ${APP_DIR}/.next
                    rm -f ${APP_DIR}/package-lock.json

                    # Copy latest files
                    rsync -av --delete --exclude='.git' --exclude='node_modules' ./ ${APP_DIR}/

                    # Go to app directory
                    cd ${APP_DIR}

                    # Load Node environment (if using nvm)
                    export NVM_DIR="${NVM_DIR}"
                    [ -s "\$NVM_DIR/nvm.sh" ] && \. "\$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}

                    # Install dependencies
                    npm install --no-audit --no-fund

                    # Build Next.js app
                    npm run build
                """
            }
        }
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
    }
}
