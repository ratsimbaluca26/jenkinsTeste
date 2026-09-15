pipeline {

    agent any

    stages {

        stage('Test') {
            steps {
                echo 'Testing application...'

                sh 'test -f app/index.html'
                sh 'grep -q "Nextidea Company" app/index.html'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                sh 'docker build -t nextidea-app:latest .'
            }
        }

        stage('Docker Test') {
            steps {
                echo 'Testing Docker image...'

                sh 'docker images | grep nextidea-app'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'

                sh 'docker compose up -d --build'
            }
        }
    }

    post {

        success {
            echo 'CI/CD SUCCESS'
        }

        failure {
            echo 'CI/CD FAILURE'
        }
    }
}