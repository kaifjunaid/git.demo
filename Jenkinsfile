pipeline {
    agent any

    options {
        durabilityHint('MAX_SURVIVABILITY')
        timestamps()
        disableConcurrentBuilds()
    }
parameters {
    gitParameter(
        name: 'BRANCH_TO_DEPLOY', 
        type: 'PT_BRANCH',
        branchFilter: '.*', // This captures main, testing, and staging
        defaultValue: 'main',
        sortMode: 'ASCENDING_SMART', 
        description: 'Git branch to deploy from'
    )
}



    environment {
        APP_DIR = '/var/www/nextjs-app'
        PORT = '3000'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

       stage('Clone Repo') {
         steps {
             echo "Deploying branch: ${params.BRANCH_TO_DEPLOY}"

             git(
                url: 'https://github.com/kaifjunaid/git.demo.git',
                branch: "${params.BRANCH_TO_DEPLOY}"
        )
    }
}


        stage('Install & Build') {
            steps {
                sh '''
                    rsync -av --delete \
                      --exclude='.git' \
                      --exclude='node_modules' \
                      ./ $APP_DIR

                    cd $APP_DIR
                    npm ci --no-audit --no-fund
                    npm run build
                '''
            }
        }

        stage('Start App (PM2)') {
            steps {
                sh '''
                    cd $APP_DIR

                    pm2 delete nextjs-app || true

                    pm2 start npm \
                      --name nextjs-app \
                      -- start -- -H 0.0.0.0 -p $PORT 

                    pm2 save
                '''
            }
        }
    }
}
