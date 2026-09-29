pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-devops-app'
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
                    Write-Host "USER=$env:DOCKER_USER"
                    Write-Host "TOKEN_LENGTH=$($env:DOCKER_TOKEN.Length)"
                    Write-Host "TOKEN_PREFIX_OK=$($env:DOCKER_TOKEN.StartsWith('dckr_pat_'))"
                    Write-Host "TRIMMED_LENGTH=$($env:DOCKER_TOKEN.Trim().Length)"
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
