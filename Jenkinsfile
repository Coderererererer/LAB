pipeline {
    agent any
    options { timestamps() }
    environment { SCANNER_HOME = tool 'sonar-scanner' }
    
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Setup') {
            steps { 
                // Обязательно в одинарных кавычках, иначе Groovy выдаст ошибку
                sh 'python3 -m venv venv && ./venv/bin/pip install --upgrade pip && ./venv/bin/pip install -r requirements.txt' 
            }
        }
        stage('Build') {
            steps { sh './venv/bin/python -m py_compile app.py' }
        }
        stage('Test') {
            steps { sh './venv/bin/pytest --junitxml=result.xml --cov=app --cov-report=xml:coverage.xml' }
        }
        stage('SonarQube Analysis') {
            steps { withSonarQubeEnv('sonarqube') { sh "${SCANNER_HOME}/bin/sonar-scanner" } }
        }
        stage('Quality Gate') {
            steps { timeout(time: 5, unit: 'MINUTES') { waitForQualityGate abortPipeline: true } }
        }
    }
    
    post {
        always {
            // Обязательно оборачиваем в node, иначе упадёт с MissingContextVariableException
            node{
                junit 'result.xml'
            }
        }
        success {
            echo 'Сборка прошла успешно, Quality Gate пройден'
        }
        failure {
            echo 'Сборка завершилась с ошибкой'
        }
    }
}
