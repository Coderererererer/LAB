pipeline {
    agent any
    options {
        timestamps()
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Setup') {
            steps {
                // Объединено в одну строку и обернуто в одинарные кавычки
                sh 'python3 -m venv venv && ./venv/bin/pip install --upgrade pip && ./venv/bin/pip install -r requirements.txt'
            }
        }
        stage('Build') {
            steps {
                sh './venv/bin/python -m py_compile app.py'
            }
        }
        stage('Test') {
            steps {
                // Объединено в одну строку
                sh './venv/bin/pytest --junitxml=result.xml --cov=app --cov-report=xml:coverage.xml'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
    post {
        always {
            // Обязательно оборачиваем в node с меткой, иначе упадет
            node('master') {
                junit 'result.xml'
            }
        }
        success {
            // Объединено в одну строку
            echo 'Сборка прошла успешно, Quality Gate пройден'
        }
        failure {
            echo 'Сборка завершилась с ошибкой'
        }
    }
}
