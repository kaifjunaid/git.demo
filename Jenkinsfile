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
        sh """
          # ... your deploy script ...
        """
      }
    }
  }

  post {
    always {
      // Wrap cleanup inside node block so workspace/FilePath context is available
      node {
        echo 'Cleaning workspace (post-build)...'
        deleteDir()
        // If you want to clean additional Jenkins-internal temp dirs:
        dir("${env.WORKSPACE}@tmp") { deleteDir() }
        dir("${env.WORKSPACE}@script") { deleteDir() }
        dir("${env.WORKSPACE}@script@tmp") { deleteDir() }
      }
    }
  }
}
