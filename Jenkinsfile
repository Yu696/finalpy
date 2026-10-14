pipeline {
    agent any

    environment {
        PYTHON_PATH = '"C:\\Users\\lenovo\\AppData\\Local\\Programs\\Python\\Python312\\python.exe"'
        http_proxy  = "http://127.0.0.1:7897"
        https_proxy = "http://127.0.0.1:7897"
        no_proxy    = "localhost,127.0.0.1"
    }

    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                    @echo off
                    echo "=== 验证Python路径及版本 ==="
                    ${PYTHON_PATH} --version
                    echo "Python路径验证通过！"
                """
            }
        }
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Yu696/finalpy.git', branch: 'main'
            }
        }
        stage('Fix pip') {
            steps {
                bat """
                    @echo off
                    echo "=== 修复Python的pip环境 ==="
                    ${PYTHON_PATH} -m ensurepip --upgrade
                    ${PYTHON_PATH} -m pip --version
                """
            }
        }
        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    ${PYTHON_PATH} -m pip install --upgrade pip
                    ${PYTHON_PATH} -m pip install -r requirements.txt
                """
            }
        }
        stage('Lint') {
            steps {
                bat "${PYTHON_PATH} -m pip install flake8 && ${PYTHON_PATH} -m flake8 app.py tests/"
            }
        }
        stage('Test') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pytest pytest-cov
                    ${PYTHON_PATH} -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Build') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pyinstaller
                    ${PYTHON_PATH} -m PyInstaller --onefile app.py
                """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                bat "start ${PYTHON_PATH} app.py"
            }
        }
    }
    post {
        success { echo 'CI/CD pipeline completed successfully!' }
        failure { echo 'CI/CD pipeline failed!' }
    }
}