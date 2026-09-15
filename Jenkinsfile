pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'test -f app/index.html'
            }
        }

        stage('Test') {
            steps {
                sh 'grep -q "Nextidea Company" app/index.html'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t nextidea-app:latest .'
            }
        }

        stage('Docker Test') {
            steps {
                // Utilisation du port 8085 pour le test éphémère afin d'éviter les conflits avec le port 8084
                sh 'docker rm -f nextidea-test 2>/dev/null || true'
                sh 'docker run -d --name nextidea-test -p 8085:80 nextidea-app:latest'
                sh 'sleep 3'
                sh 'curl -f http://localhost:8085'
            }
            post {
                always {
                    // Supprime le conteneur de test même en cas d'échec du curl
                    sh 'docker rm -f nextidea-test 2>/dev/null || true'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Verify') {
            steps {
                sh 'sleep 3'
                sh 'curl -f http://localhost:8084'
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