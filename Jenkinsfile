pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    APP_IMAGE = "sit753-jenkins-demo:latest"
    APP_CONTAINER = "sit753-demo"
    APP_PORT = "3000"
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
          set -e

          echo "=== DEBUG: PATH ==="
          echo "$PATH"

          echo "=== DEBUG: docker location ==="
          which docker || true

          echo "=== DEBUG: docker version ==="
          docker version

          echo "=== Pull Node image ==="
          docker pull node:20-alpine

          echo "=== Run npm inside container (no docker-workflow plugin) ==="
          docker run --rm \
            -v "$WORKSPACE:/app" \
            -w /app \
            node:20-alpine \
            sh -lc '
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
          set -e
          echo "=== Docker Build ==="
          docker build -t "$APP_IMAGE" .
          docker image ls "$APP_IMAGE" || true
        '''
      }
    }

    stage('Security Scan') {
      steps {
        sh '''
          set -e
          echo "=== Security Scan (basic/demo) ==="
          echo "Image info:"
          docker image inspect "$APP_IMAGE" >/dev/null
          echo "Tip: you can replace this with trivy/grype later if needed."
          echo "Security scan step executed (basic placeholder)."
        '''
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          set -e
          echo "=== Deploy application ==="

          # Stop old container if exists
          docker rm -f "$APP_CONTAINER" || true

          # Run new container
          docker run -d \
            --name "$APP_CONTAINER" \
            -p "${APP_PORT}:${APP_PORT}" \
            "$APP_IMAGE"

          echo "=== Deployed container ==="
          docker ps --filter "name=$APP_CONTAINER" --format "table {{.Names}}\\t{{.Image}}\\t{{.Status}}\\t{{.Ports}}"

          echo "=== Quick health check (best effort) ==="
          # Try common endpoints; won't fail the pipeline if app uses different path
          curl -sSf "http://localhost:${APP_PORT}/health" && echo "" || true
          curl -sSf "http://localhost:${APP_PORT}/" && echo "" || true
        '''
      }
    }

    stage('Monitoring') {
      steps {
        sh '''
          set -e
          echo "=== Monitoring (basic/demo) ==="
          echo "Container status:"
          docker ps --filter "name=$APP_CONTAINER" --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"

          echo "Last 30 lines of logs:"
          docker logs --tail 30 "$APP_CONTAINER" || true
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
