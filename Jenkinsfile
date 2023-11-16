pipeline {
    agent any
    stages {
        stage('Depot') {
            steps {
                script {
                    try {
                        checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Jean-Coignard/Jenkins.git']]])
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
    
    post {
        success {
            emailext body: 'Le build a réussi.',
                    subject: 'Succès du build',
                    to: 'jeancoignard@gmail.com'
        }
        failure {
            emailext body: 'Le build a échoué.',
                    subject: 'Échec du build',
                    to: 'jeancoignard@gmail.com'
        }
    }
}
