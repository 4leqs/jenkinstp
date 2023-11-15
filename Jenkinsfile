pipeline {
  agent any
  stages {
    stage('Depot') {
      steps {
        script {
          try {
            sh 'git clone https://github.com/Jean-Coignard/Jenkins.git'
          } catch (Exception e) {
            currentBuild.result = 'FAILURE'
            error "Erreur lors du clonage du dépôt : ${e.message}"
          }
        }

      }
    }

  }
}