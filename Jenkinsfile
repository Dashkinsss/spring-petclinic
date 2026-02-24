pipeline {
  agent any

  triggers {
    // every 5 minutes on Mondays
    cron('H/2 * * * *')
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

    stage('Build & Unit Tests') {
      steps {
        sh 'chmod +x mvnw'
        sh './mvnw -B clean test'
      }
      post {
        always {
          junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
        }
      }
    }

    stage('JaCoCo Coverage Report') {
      steps {
        // generates: target/site/jacoco/index.html
        sh './mvnw -B jacoco:report'
      }
      post {
        always {
          archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
        }
      }
    }

    stage('Package Artifact') {
      steps {
        sh './mvnw -B -DskipTests package'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
  }
}
