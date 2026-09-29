pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haroonashar/flask-app'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
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
                bat '''
                    docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS_ID}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    powershell '''
                        $user = $env:DOCKER_USER.Trim()
                        $token = $env:DOCKER_TOKEN.Trim()

                        Write-Host "Docker username: [$user]"
                        Write-Host "Username length: $($user.Length)"
                        Write-Host "Token length: $($token.Length)"

                        if ([string]::IsNullOrWhiteSpace($user)) {
                            Write-Error "Docker username is empty"
                            exit 1
                        }

                        if ([string]::IsNullOrWhiteSpace($token)) {
                            Write-Error "Docker token/password is empty"
                            exit 1
                        }

                        $token | docker login `
                            --username $user `
                            --password-stdin

                        if ($LASTEXITCODE -ne 0) {
                            Write-Error "Docker Hub login failed"
                            exit $LASTEXITCODE
                        }

                        Write-Host "Docker Hub login successful"
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat '''
                    docker push %DOCKER_IMAGE%:%BUILD_NUMBER%
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the stage logs above.'
        }

        always {
            bat 'docker logout || exit /b 0'
        }
    }
}
