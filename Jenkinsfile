node {

    def appDir = '/var/www/nextjs-app'

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins Workspace...'
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning the repository...'
        git(
            branch: 'main',
            url: 'https://github.com/kaifjunaid/git.demo.git'
        )
    }

    stage('Deploy to EC2') {
        echo 'Deploying to EC2...'

        sh """
            set -e

            echo "===== Preparing Deployment Directory ====="
            sudo mkdir -p ${appDir}
            sudo chown -R jenkins:jenkins ${appDir}

            echo "===== Syncing Code ====="
            rsync -av --delete \
                --exclude='.git' \
                --exclude='node_modules' \
                ./ ${appDir}/

            cd ${appDir}

            echo "===== Installing Dependencies ====="
        """

        // Retry npm install for reliability
        retry(3) {
            sh "cd ${appDir} && npm install --no-audit --no-fund"
        }

        sh """
            echo "===== Building App ====="
            cd ${appDir}
            npm run build

            echo "===== Restarting App on Port 3000 ====="
            fuser -k 3000/tcp || true

           
            nohup npm run start > app.log 2>&1 &
        """
    }
}
