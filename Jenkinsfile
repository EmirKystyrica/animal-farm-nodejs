pipeline {
    agent any

    environment {
        TELEGRAM_BOT_TOKEN = '7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0'
        TELEGRAM_CHAT_ID = '292560946'
        PROJECT_NAME = 'animal-farm-nodejs'
    }

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
                script {
                    // Запуск сервера на фоне
                    bat 'start /B npx cross-env PORT=3001 node app.js'
                    sleep time: 10, unit: 'SECONDS'
                    // Завершение node-процесса
                    bat 'taskkill /im node.exe /f'
                }
            }
        }

        stage('Notify Success') {
            steps {
                powershell '''
                    $text = "✅ Сборка УСПЕШНА | Проект: ${env.PROJECT_NAME} | Сборка #${env.BUILD_NUMBER} | Jenkins: http://localhost:8080/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/"
                    $uri = "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage"
                    $body = @{ chat_id = "${env.TELEGRAM_CHAT_ID}"; text = $text }
                    Invoke-RestMethod -Uri $uri -Method Post -Body $body
                '''
            }
        }
    }

    post {
        failure {
            powershell '''
                $text = "❌ Сборка ПРОВАЛЕНА | Проект: ${env.PROJECT_NAME} | Сборка #${env.BUILD_NUMBER} | Jenkins: http://localhost:8080/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/"
                $uri = "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage"
                $body = @{ chat_id = "${env.TELEGRAM_CHAT_ID}"; text = $text }
                Invoke-RestMethod -Uri $uri -Method Post -Body $body
            '''
        }
    }
}
