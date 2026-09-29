pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-app'
        DOCKER_USERNAME = 'haroonashar'

        // TEMPORARY TEST ONLY
        // Paste your Docker Hub access token here.
        DOCKER_PASSWORD = 'PASTE_YOUR_DOCKER_HUB_TOKEN_HERE'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                bat 'python --version'
                bat 'python -m pip --version'
                bat 'docker --version'
            }
        }

        stage('Setup') {
            steps {
                bat 'python -m pip install --upgrade pip'
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
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                // IMPORTANT:
                // no spaces before the pipe
                bat 'echo %DOCKER_PASSWORD%|docker login -u %DOCKER_USERNAME% --password-stdin'
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the failed stage logs.'
        }

        always {
            bat 'docker logout'
        }
    }
}
