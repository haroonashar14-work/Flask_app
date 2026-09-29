stage('Check Environment') {
    steps {
        bat 'python --version'
        bat 'python -m pip --version'
    }
}
pipeline {
    agent any

    stages {
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
    }
}