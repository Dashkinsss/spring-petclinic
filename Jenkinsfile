pipeline {
  agent any

  triggers {
    // TEMP for testing: every 2 minutes (any day)
    cron('H/2 * * * *')
    // For submission switch back:
    // cron('H/5 * * * 1')
  }

  options {
    timestamps()
    disableConcurrentBuilds()
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
        // run tests to generate surefire + jacoco exec data
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
        // generate HTML report: target/site/jacoco/index.html
        sh './mvnw -B jacoco:report'
      }
      post {
        always {
          // 1) Publish JaCoCo into Jenkins UI (requires JaCoCo plugin)
          jacoco(
            execPattern: 'target/*.exec',
            classPattern: 'target/classes',
            sourcePattern: 'src/main/java',
            changeBuildStatus: false
          )

          // 2) Also archive the HTML report (works even without plugin)
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
