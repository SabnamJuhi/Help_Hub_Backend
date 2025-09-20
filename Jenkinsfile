pipeline {
  agent {
    docker {
      image 'node:18'   // Use official Node.js image
      args '-p 3000:3000'  // optional: expose ports if needed
    }
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/SabnamJuhi/Help_Hub_Backend.git'
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