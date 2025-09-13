pipeline {
  agent any
  tools { jdk 'jdk17' }  // name must match Jenkins tool
  options { timestamps() }

  environment {
    IMAGE_NAME = "vulnapp"
    IMAGE_TAG  = "jenkins-${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build JAR (Gradle)') {
      steps {
        sh '''
          set -e
          ./gradlew clean build
        '''
      }
    }

    stage('Build Docker image with Jib') {
      steps {
        sh '''
          set -e
          ./gradlew jibDockerBuild -Dimage=${IMAGE_NAME}:${IMAGE_TAG}
          echo "Built image: ${IMAGE_NAME}:${IMAGE_TAG}"
        '''
      }
    }

    // Optional: smoke-run the container briefly (requires /var/run/docker.sock mounted)
    stage('Smoke test (optional)') {
      when { expression { return fileExists('docker-compose.yml') } }
      steps {
        sh '''
          set -e
          # just show images/compose, don’t keep it running for now
          docker images | head -n 10 || true
          docker compose version || docker-compose version || true
        '''
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
      echo "✅ Image ${IMAGE_NAME}:${IMAGE_TAG} built"
    }
  }
}

