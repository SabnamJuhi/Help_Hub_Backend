pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/<YOUR_GH_USERNAME>/<REPO_NAME>.git'
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

    stage('Trigger Render Deploy') {
      steps {
        withCredentials([string(credentialsId: 'render_hook', variable: 'RENDER_HOOK')]) {
          sh 'curl -X POST "$RENDER_HOOK"'
        }
      }
    }
  }
}
