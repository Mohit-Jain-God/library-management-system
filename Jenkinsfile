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

        stage('Select Python') {
            steps {
                script {
                    def candidates = [
                        'python',
                        'py -3',
                        'py',
                        '"C:\\Program Files\\Python313\\python.exe"',
                        '"C:\\Program Files\\Python312\\python.exe"',
                        '"C:\\Program Files\\Python311\\python.exe"',
                        '"C:\\Program Files\\Python310\\python.exe"',
                        '"C:\\Users\\realm\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"',
                        '"C:\\Users\\realm\\AppData\\Local\\Programs\\Python\\Python312\\python.exe"',
                        '"C:\\Users\\realm\\AppData\\Local\\Programs\\Python\\Python311\\python.exe"',
                        '"C:\\Users\\mohit\\AppData\\Local\\Programs\\Python\\Python312\\python.exe"',
                        '"C:\\Users\\mohit\\AppData\\Local\\Programs\\Python\\Python311\\python.exe"'
                    ]

                    def found = null
                    for (cmd in candidates) {
                        def status = bat(returnStatus: true, script: "${cmd} --version >nul 2>nul")
                        if (status == 0) {
                            found = cmd
                            break
                        }
                    }

                    if (found == null) {
                        error('Python not found. Install Python and/or add it to PATH for Jenkins service.')
                    }

                    env.PY_CMD = found
                    echo "Using Python command: ${env.PY_CMD}"
                }
            }
        }

        stage('Install') {
            steps {
                bat "${env.PY_CMD} -m pip install --upgrade pip"
                bat "${env.PY_CMD} -m pip install -r requirements.txt"
                bat "${env.PY_CMD} -m pip install flake8 pytest"
            }
        }

        stage('Lint') {
            steps {
                bat "${env.PY_CMD} -m flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics"
                bat "${env.PY_CMD} -m flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics"
            }
        }

        stage('Test') {
            steps {
                bat "${env.PY_CMD} -m pytest tests/test_app.py --junitxml=results.xml"
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

