pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'test -f app/index.html'
            }
        }

        stage('Static Test') {
            steps {
                sh 'grep -q "Nextidea Company" app/index.html'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t nextidea-app:latest .'
            }
        }

        stage('Test Container') {
            steps {
                // 1. Nettoyage d'un éventuel ancien conteneur de test
                sh 'docker rm -f nextidea-test || true'
                
                // 2. Démarrage du conteneur éphémère
                sh 'docker run -d --name nextidea-test nextidea-app:latest'
                
                // 3. Attente du démarrage de Nginx
                sh 'sleep 3'
                
                // 4. Test interne de l'application
                sh 'docker exec nextidea-test wget --spider -q http://localhost:80 || docker exec nextidea-test curl -f http://localhost:80'
                
                // 5. Nettoyage après test
                sh 'docker rm -f nextidea-test'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'sleep 5'
                // Vérification du statut des services lancés par compose
                sh 'docker compose ps'
            }
        }
    }

    post {

        success {
            echo 'CI/CD terminé avec succès'
        }

        failure {
            echo 'CI/CD échoué'
        }
    }
}