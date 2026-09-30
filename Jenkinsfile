pipeline {
agent any
options {
timestamps()

практика 1. установка и настройка jenkins 3

}
stages {
stage('Checkout') {
steps {
checkout scm
}
}
stage('Setup') {
steps {
sh 'python3 -m venv venv'
sh './venv/bin/pip install --upgrade pip'
sh './venv/bin/pip install -r requirements.txt'
}
}
stage('Build') {
steps {
sh './venv/bin/python -m py_compile app.py'
}
}
stage('Test') {
steps {
sh './venv/bin/pytest --junitxml=result.xml'
}
}
}
post {
always {
junit 'result.xml'
}
success {
echo 'Сборка прошла успешно'
}
failure {

практика 1. установка и настройка jenkins 4

echo 'Сборка завершилась с ошибкой'
}
}
}
