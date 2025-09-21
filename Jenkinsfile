pipeline {
  agent {
    docker {
      image 'node:18'
      args '-p 3000:3000'
    }
  }

  stages {
    stage('Debug Branch') {
            steps {
                echo "Detected branch name: ${env.BRANCH_NAME}"
            }
    }
    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Test') {
      steps {
        sh 'npm test || true'
      }
    }

    stage('Deploy to Dev') {
      when { expression { env.BRANCH_NAME == 'origin/dev' } }
      steps {
        withCredentials([string(credentialsId: 'render_hook_dev', variable: 'RENDER_HOOK')]) {
          sh "curl -s -X POST \"$RENDER_HOOK\""
        }
      }
    }

    stage('Deploy to Staging') {
      when { expression { env.BRANCH_NAME == 'origin/test' } }
      steps {
        withCredentials([string(credentialsId: 'render_hook_test', variable: 'RENDER_HOOK')]) {
          sh "curl -s -X POST \"$RENDER_HOOK\""
        }
      }
    }

    stage('Deploy to Production') {
      when { expression { env.BRANCH_NAME == 'origin/main' } }
      steps {
        input message: "Approve deploy to Production?", ok: "Deploy"
        withCredentials([string(credentialsId: 'render_hook_prod', variable: 'RENDER_HOOK')]) {
          sh "curl -s -X POST \"$RENDER_HOOK\""
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline succeeded."
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
