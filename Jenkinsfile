pipeline {
    agent any
    stages {
        stage('Depot') {
            steps {
                script {
                    try {
                        checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/4leqs/jenkinstp.git']]])
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Erreur lors du clonage du dépôt : ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Construire') {
            steps {
                script {
                    try {
                        dir('Jenkins') {
                            sh 'docker build -t docker-image-test .'
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Erreur lors de la construction : ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Trivy') {
            steps {
                script {
                    try {
                        sh 'docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image docker-image-test'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Erreur lors de l'analyse avec Trivy : ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Deployer') {
            when {
                expression {
                    currentBuild.resultIsBetterOrEqualTo('SUCCESS')
                }
            }
            steps {
                script {
                    try {
                        def container = docker.image('docker-image-test').run("--name test_auto -p 8000:8080 -d")
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Erreur lors du déploiement : ${e.getMessage()}"
                    }
                }
            }
        }
    }
    
        stage('Suppresion Image') {
              steps {
                sh '''docker ps -a'''
                sleep 90
                sh 'docker rm --force test-auto-jenkins'
                sh 'docker ps -a'
              }
            }
    
    post {
    always {
      script {
        discordSend(
          description: "Le build a ${currentBuild.result}",
          result: currentBuild.result,
          title: env.JOB_NAME,
          webhookURL: "https://discordapp.com/api/webhooks/1174703049139310612/aICX4eQDVVeqmNNyKQXuIyNIdeQ5_Oq5ScER3Rspdom8LlNCwLYbhg8Bcb9TKYL3NBgl")
        }

      }

}
}
