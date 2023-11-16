pipeline {
  agent any
  stages {
    stage('Cloner le dépôt') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: '']]])
      }
    }

    stage('Construire l\'image Docker') {
      steps {
        script {
          docker.build('test-image-jenkins')
        }

      }
    }

  }
