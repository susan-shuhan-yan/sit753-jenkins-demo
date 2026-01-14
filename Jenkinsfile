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

    stage('Build & Test (Node)') {
      agent {
        docker {
          image 'node:20-alpine'
        }
      }
      steps {
        sh '''
          node -v
          npm -v
          npm ci
          npm test
          npm run build
        '''
      }
    }

    stage('Docker Build') {
      agent {
        docker {
          image 'docker:27-cli'
          args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
      }
      steps {
        sh '''
          docker build -t $IMAGE_NAME .
        '''
      }
    }

    stage('Security Scan') {
      agent {
        docker {
          image 'aquasec/trivy:latest'
          args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
      }
      steps {
        sh '''
          trivy image $IMAGE_NAME || true
        '''
      }
    }

    stage('Deploy') {
      agent {
        docker {
          image 'docker:27-cli'
          args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
      }
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
