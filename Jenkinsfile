pipeline {
    agent any

    stages {
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/azmahboub/Atelier2_pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("app_web1:${env.BUILD_ID}")
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Arrêter et supprimer l'ancien conteneur s'il existe
                    sh 'docker stop app_web1_demo || true'
                    sh 'docker rm app_web1_demo || true'
                    // Lancer le nouveau conteneur
                    dockerImage.run("-d -p 8081:80 --name app_web1_demo")
                }
            }
        }

        stage('Test Access') {
            steps {
                sh 'sleep 2' // laisser le temps à Nginx de démarrer
                sh 'curl --fail http://172.18.0.1:8081'
            }
        }
    }

    post {
        success {
            echo 'Pipeline réussi ! L\'application est accessible sur http://<jenkins-host>:8081'
        }
        failure {
            echo 'Pipeline échoué, vérifiez les logs.'
        }
    }
}
