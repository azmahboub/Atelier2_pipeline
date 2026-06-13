pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/azmahboub/app_web1.git'
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
                sh 'curl --fail http://localhost:8081'
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