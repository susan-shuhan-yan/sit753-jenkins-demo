pipeline {
  agent {
    docker {
      image 'node:20-alpine'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
  }

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
