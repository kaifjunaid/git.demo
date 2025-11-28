pipeline {
  agent any

  stages {
    stage('Clean Workspace') {
      steps {
        echo 'Cleaning Jenkins workspace (before build)'
        cleanWs()
      }
    }

    stage('Clone Repo') {
      steps {
        git branch: 'main',
            url: 'https://github.com/kaifjunaid/git.demo.git'
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
          # ... your deploy script ...
        """
      }
    }
  }

  post {
    always {
      echo 'Cleaning workspace (after build regardless of result)'
      cleanWs(deleteDirs: true, disableDeferredWipeout: true, notFailBuild: true) 
    }
  }
} 
