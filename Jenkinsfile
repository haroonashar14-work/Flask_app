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
        
        stage('Check Docker Credential') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_TOKEN'
            )
        ]) {
            powershell '''
                $bytes = [System.Text.Encoding]::UTF8.GetBytes($env:DOCKER_TOKEN)
                $sha = [System.Security.Cryptography.SHA256]::Create()
                $hash = [BitConverter]::ToString($sha.ComputeHash($bytes)).Replace("-", "").ToLower()

                Write-Host "USER=$env:DOCKER_USER"
                Write-Host "LENGTH=$($env:DOCKER_TOKEN.Length)"
                Write-Host "SHA256=$hash"
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
