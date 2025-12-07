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
                    # Create and set ownership on app directory
                    sudo mkdir -p ${APP_DIR}
                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    # Sync project files
                    rsync -av --delete \
                        --exclude='.git' \
                        --exclude='node_modules' \
                        ./ ${APP_DIR}/

                    # Move into app directory
                    cd ${APP_DIR}

                    # Kill any app already running on port 3000
                    sudo fuser -k 3000/tcp || true

                    # Install dependencies without heavy logs
                    npm install --no-audit --no-fund --silent

                    # Build Next.js app
                    npm run build

                    # Stop previous running app
                    pkill -f 'npm run start' || true

                    # Start app in background safely
                    nohup npm run start > app.log 2>&1 &
                """ 
            }
        }
    }
}
