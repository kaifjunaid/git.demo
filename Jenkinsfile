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
      // full cleanup after build
      deleteDir()
      dir("${env.WORKSPACE}@tmp") { deleteDir() }
      dir("${env.WORKSPACE}@script") { deleteDir() }
      dir("${env.WORKSPACE}@script@tmp") { deleteDir() }
    }
  }
}