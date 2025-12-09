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
                git(url: 'https://github.com/kaifjunaid/git.demo.git', branch: 'main')
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying latest version to EC2...'
                sh """
                    echo "Preparing deployment directory..."
                    sudo mkdir -p ${APP_DIR}
                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    echo "Removing old node_modules & lock file..."
                    rm -rf ${APP_DIR}/node_modules
                    rm -f ${APP_DIR}/package-lock.json

                    echo "Syncing latest files..."
                    rsync -av --delete \
                        --exclude='.git' \
                        --exclude='node_modules' \
                        ./ ${APP_DIR}/

                    echo "Installing dependencies (verbose)..."
                    cd ${APP_DIR}
                    npm install --verbose --no-audit --no-fund

                    echo "Building application..."
                    npm run build

                    echo "Killing old app..."
                    sudo fuser -k 3000/tcp || true

                    echo "Starting application..."
                    nohup npm start > app.log 2>&1 &
                """
            }
        }
    }
}
