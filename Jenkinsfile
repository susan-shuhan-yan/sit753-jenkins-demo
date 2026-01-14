pipeline {
  agent any

  environment {
    // Ensure docker CLI is found in Jenkins shell environment
    PATH = "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    IMAGE_NODE = "node:20-alpine"
    APP_IMAGE  = "sit753-jenkins-demo:latest"
  }

  options {
    timestamps()
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Test (Node)') {
      steps {
        sh '''
          echo "=== DEBUG: PATH ==="
          echo "$PATH"

          echo "=== DEBUG: docker location ==="
          which docker

          echo "=== DEBUG: docker version ==="
          docker version

          echo "=== Pull Node image ==="
          docker pull ${IMAGE_NODE}

          echo "=== Run npm inside container (no docker-workflow plugin) ==="
          # Mount Jenkins workspace into /app, run npm ci/install + test
          docker run --rm \
            -v "$PWD:/app" \
            -w /app \
            ${IMAGE_NODE} sh -lc '
              node -v
              npm -v
              npm ci || npm install
              npm test || true
            '
        '''
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
          echo "=== Docker Build ==="
          docker build -t ${APP_IMAGE} .
        '''
      }
    }

    stage('Security Scan') {
      steps {
        sh '''
          echo "=== Security Scan (demo placeholder) ==="
          # If you want a real scanner, install trivy and replace below:
          # trivy image --severity HIGH,CRITICAL ${APP_IMAGE} || true
          docker image inspect ${APP_IMAGE} > /dev/null
          echo "Security scan step executed (placeholder)."
        '''
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          echo "=== Deploy (demo) ==="
          echo "Deploy step executed (placeholder)."
        '''
      }
    }

    stage('Monitoring') {
      steps {
        sh '''
          echo "=== Monitoring (demo) ==="
          echo "Monitoring step executed (placeholder)."
        '''
      }
    }
  }

  post {
    always {
      echo "Done ✅"
    }
  }
}
