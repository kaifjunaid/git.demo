pipeline {
  agent any
  options {
    // Skip Jenkins' automatic SCM checkout — so we can clean workspace first
    skipDefaultCheckout(true)
  }
  stages {
    stage('Prepare') {
      steps {
        echo 'Cleaning workspace before checkout…'
        cleanWs()                // clean entire workspace
        checkout scm             // then fetch the repo
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
  post {
    always {
      echo 'Cleaning workspace after build…'
      cleanWs(deleteDirs: true, disableDeferredWipeout: true)
    }
  }
}
