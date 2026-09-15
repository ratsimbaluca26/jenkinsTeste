```groovy
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
                sh 'docker run -d --name nextidea-test -p 8084:80 nextidea-app:latest'
                sh 'sleep 3'
                sh 'curl -f http://localhost:8084'
                sh 'docker rm -f nextidea-test'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Verify') {
            steps {
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
```