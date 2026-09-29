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

        stage('Login to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    powershell '''
                        $env:DOCKER_TOKEN | docker login -u $env:DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'
            }
        }
    }
}
