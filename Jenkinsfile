def appDir = '/var/www/nextjs-app'

pipeline {
    agent any

    stages { 

        stage('Clean Workspace') {
            steps {
                echo 'Cleaning Jenkins Workspace' 
                deleteDir()
            }
        }

        stage('Clone Repo') {
            steps {
                echo 'Cloning the repo'
                git branch: 'main', url: 'https://github.com/kaifjunaid/git.demo.git'
            }
        }

        stage('Build on Jenkins') {
            steps {
                sh '''
                    npm install
                    npm run build
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Deploying to EC2'
                sh """
                    sudo mkdir -p ${appDir}
                    sudo chown -R jenkins:jenkins ${appDir}

                    rsync -av --delete --exclude='.git' ./ ${appDir}

                    cd ${appDir}
                    npm install --omit=dev

                    sudo fuser -k 3000/tcp || true   
                    npm run start &
                """
            }
        }
    }
} 