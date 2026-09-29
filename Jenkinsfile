pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-app'
    }

    stages {
        stage('Check Environment') {
            steps {
                bat 'python --version'
                bat 'python -m pip --version'
                bat 'docker --version'
            }
        }

        stage('Setup') {
            steps {
                bat 'python -m pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                bat 'python -m pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .'
            }
        }

               stage('Check Environment') {
            steps {
                bat 'whoami'
                bat 'python --version'
                bat 'python -m pip --version'
                bat 'docker --version'
            }
        }
        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'
            }
        }
    }
}
