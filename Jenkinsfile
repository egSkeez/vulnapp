pipeline {
  agent any

  tools { jdk 'jdk17' } // <-- name exactly as in Manage Jenkins > Tools

  environment {
    JAVA_HOME = "${tool 'jdk17'}"
    PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
  }

  options { timestamps() }

  stages {
    stage('Verify Java') {
      steps {
        sh '''
          echo "Using JAVA_HOME=$JAVA_HOME"
          java -version
        '''
      }
    }

    stage('Clean Gradle caches (one-time if needed)') {
      steps {
        sh '''
          # remove caches compiled under the wrong JDK (class file version 65)
          rm -rf ~/.gradle/caches || true
          rm -rf ~/.gradle/daemon || true
          rm -rf .gradle || true
        '''
      }
    }

    stage('Build') {
      steps {
        sh '''
          ./gradlew --no-daemon --version
          ./gradlew --no-daemon -Dorg.gradle.java.home="$JAVA_HOME" clean build
        '''
      }
    }
  }

  post {
    always { echo 'Done' }
  }
}

