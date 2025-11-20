pipeline {
  agent any

  triggers {
    // Works behind Vagrant/private network (polls GitHub every minute)
    pollSCM('* * * * *')

    // If you expose Jenkins via ngrok or port-forwarding, enable GitHub webhook:
    // githubPush()
  }

  stages {

    /* ==========================================
     * CHECKOUT
     * ========================================== */
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

    /* ==========================================
     * BUILD
     * ========================================== */
    stage('Build') {
      steps {
        sh 'echo "👉 Build step goes here"'
      }
    }

    /* ==========================================
     * SONARQUBE ANALYSIS
     * ========================================== */
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh """
            echo 'Running SonarQube Analysis...'
            ${tool 'sonar-scanner'}/bin/sonar-scanner \
              -Dsonar.projectKey=student-pipeline \
              -Dsonar.sources=. \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_AUTH_TOKEN
          """
        }
      }
    }

    /* ==========================================
     * TESTS
     * ========================================== */
    stage('Test') {
      steps {
        sh 'echo "👉 Test step goes here"'
      }
    }

    /* ==========================================
     * SUMMARY
     * ========================================== */
    stage('Summary') {
      steps {
        script {
          def msg = sh(script: "git log -1 --pretty=%s", returnStdout: true).trim()
          currentBuild.displayName = "#${BUILD_NUMBER} ${env.GIT_BRANCH} ${env.GIT_COMMIT.take(7)}"
          currentBuild.description = "Commit: ${msg}"
        }
      }
    }

  } // END stages


  /* ==========================================
   * POST ACTIONS
   * ========================================== */
  post {
    success {
      echo "✅ Pipeline succeeded"
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
