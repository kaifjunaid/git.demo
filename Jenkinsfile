node {
    def appDir = '/var/www/nextjs-app' 

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins Workspace...' 
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning the repo...'
        git(
            branch: 'main',
            url: 'https://github.com/kaifjunaid/git.demo.git'
        )
    }
  options {
  durabilityHint('MAX_SURVIVABILITY')
}

    stage('Deploy to EC2') {
        echo 'Deploying to EC2...'
        sh """
            sudo mkdir -p ${appDir}
            sudo chown -R jenkins:jenkins ${appDir}

            rsync -av --delete --exclude='.git' --exclude='node_modules' ./ ${appDir}

            cd ${appDir}

           rm -rf node_modules
           npm ci --no-audit --no-fund

            npm run build

            sudo fuser -k 3000/tcp || true

            nohup npm run start > app.log 2>&1 &
        """
    }
}   
