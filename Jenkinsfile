pipeline {
  agent any
  stages {
    stage('Depot') {
      steps {
        script {
          sh 'git clone https://github.com/Jean-Coignard/Jenkins.git'
        }

      }
    }

    stage('Construction Image') {
      steps {
        script {
          sh 'wget https://github.com/4leqs/jenkinstp.git'
          sh 'docker build -t docker_tp:latest .'
        }

      }
    }

  }
  post {
    success {
      emailext(subject: 'Build Jenkins réussi', body: 'Le build a réussi.', to: 'jeancoignard@gmail.com')
    }

    failure {
      emailext(subject: 'Échec du Build Jenkins', body: 'Le Jenkins a échoué.', to: 'jeancoignard@gmail.com')
    }

  }
}