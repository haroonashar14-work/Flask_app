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
                        $user = $env:DOCKER_USER.Trim()
                        $token = $env:DOCKER_TOKEN.Trim()

                        Write-Host "Docker username: $user"
                        Write-Host "Token loaded: $($token.Length -gt 0)"

                        $processInfo = New-Object System.Diagnostics.ProcessStartInfo
                        $processInfo.FileName = "docker.exe"
                        $processInfo.Arguments = "login --username $user --password-stdin"
                        $processInfo.UseShellExecute = $false
                        $processInfo.RedirectStandardInput = $true
                        $processInfo.RedirectStandardOutput = $true
                        $processInfo.RedirectStandardError = $true

                        $process = New-Object System.Diagnostics.Process
                        $process.StartInfo = $processInfo

                        $process.Start() | Out-Null

                        $process.StandardInput.Write($token)
                        $process.StandardInput.Close()

                        $stdout = $process.StandardOutput.ReadToEnd()
                        $stderr = $process.StandardError.ReadToEnd()

                        $process.WaitForExit()

                        Write-Host $stdout

                        if ($process.ExitCode -ne 0) {
                            Write-Host $stderr
                            exit $process.ExitCode
                        }

                        Write-Host "Docker Hub login successful"
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

    post {
        always {
            bat 'docker logout || exit /b 0'
        }
    }
}
