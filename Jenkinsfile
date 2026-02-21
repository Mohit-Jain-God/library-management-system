pipeline {
    agent any

    environment {
        FLASK_APP = 'app.py'
        FLASK_ENV = 'development'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                bat 'py -m pip install --upgrade pip'
                bat 'py -m pip install -r requirements.txt'
            }
        }

        stage('Lint') {
            steps {
                bat 'py -m flake8 .'
            }
        }

        stage('Test') {
            steps {
                bat 'py -m pytest tests/test_app.py --junitxml=results.xml'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'results.xml'
                }
            }
        }
    }

    post {
        success {
            echo 'Build passed'
        }
        failure {
            echo 'Build failed'
        }
    }
}

