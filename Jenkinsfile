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

      stage('Deploy to Dev') {
      when { branch 'dev' }
      steps {
        withCredentials([string(credentialsId: 'render_hook_dev', variable: 'RENDER_HOOK')]) {
          sh "curl -s -X POST \"$RENDER_HOOK\""
        }
      }
    }

    stage('Deploy to Staging') {
      when { branch 'test' }
      steps {
        withCredentials([string(credentialsId: 'render_hook_test', variable: 'RENDER_HOOK')]) {
          sh "curl -s -X POST \"$RENDER_HOOK\""
        }
      }
    }

    stage('Deploy to Production') {
      when { branch 'main' }
      steps {
        // require manual approval
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
      always {
        archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
      }
    }
}