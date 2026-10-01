pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-app'
        KUBECONFIG = 'C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config'

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
                    bat '''
                        docker login -u "%DOCKER_USER%" -p "%DOCKER_TOKEN%"
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                    kubectl set image deployment/flask-app flask-app=haroonashar/flask-app:%BUILD_NUMBER%
                    kubectl rollout status deployment/flask-app --timeout=120s
                '''
            }
        }
        stage('Check Kubernetes') {
            steps {
                bat 'kubectl version --client'
                bat 'kubectl config current-context'
                bat 'kubectl get nodes'
            }
        }
    }

    post {
        always {
            bat 'docker logout || exit /b 0'
        }
    }
}
