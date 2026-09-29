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

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                python -m pip install --upgrade pip
                if exist requirements.txt (
                    python -m pip install -r requirements.txt
                )
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                if exist test_app.py (
                    python -m pytest -v
                ) else (
                    echo No test_app.py found. Skipping tests.
                )
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build -t %DOCKER_IMAGE%:latest .
                '''
            }
        }

        stage('Login and Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    bat '''
                    @echo off

                    echo Logging in to Docker Hub...

                    echo %DOCKER_TOKEN% | docker login -u %DOCKER_USER% --password-stdin

                    if errorlevel 1 (
                        echo Docker login failed.
                        exit /b 1
                    )

                    echo Docker login successful.

                    echo Pushing image...
                    docker push %DOCKER_IMAGE%:latest

                    if errorlevel 1 (
                        echo Docker push failed.
                        exit /b 1
                    )

                    echo Docker image pushed successfully.
                    '''
                }
            }
        }

    }

    post {

        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the logs above.'
        }

        always {
            bat '''
            docker logout
            '''
        }
    }
}
