pipeline {
  agent any

  triggers {
    // Option A: Poll GitHub every minute (works behind Vagrant/private IP)
    pollSCM('* * * * *')

    // Option B: Use GitHub webhook instead (uncomment if you expose Jenkins)
    // githubPush()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
        sh '''
          echo "JOB_NAME     : ${JOB_NAME}"
          echo "BUILD_NUMBER : ${BUILD_NUMBER}"
          echo "BRANCH       : ${GIT_BRANCH}"
          echo "COMMIT       : ${GIT_COMMIT}"

          echo "---- Latest Commit ----"
          git log -1 --pretty=format:"%h | %an <%ae> | %s"
          echo
        '''
      }
    }

    stage('Build') {
      steps {
        sh 'echo "👉 Build step goes here"'
      }
    }

    stage('Test') {
      steps {
        sh 'echo "👉 Test step goes here"'
      }
    }

    stage('Summary') {
      steps {
        script {
          def msg = sh(script: "git log -1 --pretty=%s", returnStdout: true).trim()
          currentBuild.displayName = "#${BUILD_NUMBER} ${env.GIT_BRANCH} ${env.GIT_COMMIT.take(7)}"
          currentBuild.description = "Commit: ${msg}"
        }
      }
    }
  }

  post {
    success {
      echo "✅ Pipeline succeeded"
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
