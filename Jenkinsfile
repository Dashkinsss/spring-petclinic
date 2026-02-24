pipeline {
  agent any

  triggers {
    // every 5 minutes on Mondays
    cron('H/5 * * * 1')
  }

  tools {
    // если в Jenkins настроены tool installations:
    // jdk 'JDK17'
    // maven 'Maven3'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Unit Tests') {
      steps {
        sh 'mvn -B clean test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('Jacoco Coverage Report') {
      steps {
        sh 'mvn -B jacoco:report'
      }
    }

    stage('Package Artifact') {
      steps {
        sh 'mvn -B -DskipTests package'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
  }
}
