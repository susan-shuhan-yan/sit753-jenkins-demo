pipeline {
  agent any

  environment {
    IMAGE_NAME = "sit753-demo"
    CONTAINER_NAME = "sit753-demo"
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
          npm ci
          npm run build
        '''
      }
    }

    stage('Test') {
      steps {
        sh 'npm test'
      }
    }

    stage('Security') {
      steps {
        sh '''
          docker build -t $IMAGE_NAME .
          docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy image $IMAGE_NAME || true
        '''
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          docker rm -f $CONTAINER_NAME || true
          docker run -d -p 3000:3000 --name $CONTAINER_NAME $IMAGE_NAME
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
