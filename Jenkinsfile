pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh '''
          node -v
          npm -v
          npm ci
          npm run build
        '''
      }
    }

    stage('Test') {
      steps {
        sh '''
          npm test
        '''
      }
    }
  }
}
