pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-app'
        DOCKER_USERNAME = 'haroonashar'
        DOCKER_PASSWORD = 'dckr_pat_swse5jFVQiyXAfJu1Py8RL6Y5_A'
    }

    stages {

        stage('Check Environment') {
            steps {
                bat 'python --version'
                bat 'python -m pip --version'
                bat 'docker --version'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                bat 'echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%'
            }
        }
    }
}
