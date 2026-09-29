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
                $psi = New-Object System.Diagnostics.ProcessStartInfo
                $psi.FileName = "docker"
                $psi.Arguments = "login -u `"$env:DOCKER_USER`" --password-stdin"
                $psi.UseShellExecute = $false
                $psi.RedirectStandardInput = $true
                $psi.RedirectStandardOutput = $true
                $psi.RedirectStandardError = $true

                $process = New-Object System.Diagnostics.Process
                $process.StartInfo = $psi

                $process.Start() | Out-Null

                $process.StandardInput.Write($env:DOCKER_TOKEN)
                $process.StandardInput.Close()

                $stdout = $process.StandardOutput.ReadToEnd()
                $stderr = $process.StandardError.ReadToEnd()

                $process.WaitForExit()

                Write-Host $stdout

                if ($process.ExitCode -ne 0) {
                    Write-Host $stderr
                    throw "Docker Hub login failed"
                }
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
