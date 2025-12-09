node {
    def appDir = '/var/www/nextjs-app'

    stage('clean workspace') {
        echo 'cleaning Jenkins Workspace'
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning the repo'
        git(
            branch: 'main',
            url: 'https://github.com/kaifjunaid/git.demo.git'
        )
    }

    stage('deploy to EC2') {
        echo 'Deploying to EC2'
        sh """
            sudo mkdir -p ${appDir}
            sudo chown -R jenkins:jenkins ${appDir}

            # Sync project files
            rsync -av --delete \
                --exclude='.git' \
                --exclude='node_modules' \
                ./ ${appDir}

            cd ${appDir}
            sudo npm install
            sudo npm run build

            # Stop existing Next.js process (if any)
            sudo fuser -k 3000/tcp || true

            # Start the app
            npm run start
        """
    }
}
