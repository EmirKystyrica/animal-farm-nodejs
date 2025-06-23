pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install') {
            steps {
                bat 'npm install'
            }
        }
        stage('Test') {
            steps {
                bat 'npx cross-env NODE_ENV=test PORT=3000 npm test'
            }
        }
        stage('Deploy') {
            steps {
                // Запускаем приложение в фоне
                bat 'start /B npx cross-env PORT=3001 node app.js'
                // Ждем 10 секунд чтобы приложение поднялось
                sleep 10
                // Останавливаем node процесс
                bat 'taskkill /im node.exe /f || exit 0'
            }
        }
    }

    post {
        always {
            echo "Сборка завершена. Проверяй результаты."
        }
        success {
            echo "Сборка успешно завершена!"
        }
        failure {
            echo "Сборка провалена!"
        }
    }
}
