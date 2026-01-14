pipeline {
  agent {
    docker {
      image 'docker:26-cli'
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
          apk add --no-cache nodejs npm
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

    stage('Deploy') {
      steps {
        sh '''
          docker build -t sit753-demo .
          docker rm -f sit753-demo || true
          docker run -d -p 3000:3000 --name sit753-demo sit753-demo
        '''
      }
    }

    stage('Monitoring') {
      steps {
        sh '''
          sleep 3
          curl -f http://localhost:3000/health
        '''
      }
    }
  }
}
