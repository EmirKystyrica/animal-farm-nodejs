pipeline {
    agent any

    environment {
        TELEGRAM_BOT_TOKEN = '7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0'
        TELEGRAM_CHAT_ID = '292560946'
        PROJECT_NAME = 'animal-farm-nodejs'
        NODE_ENV = 'test'
        PORT = '3000'
    }

    stages {
        stage('Checkout SCM') {
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
                    // Запускаем приложение в фоне
                    bat 'start /B npx cross-env PORT=3001 node app.js'
                    // Ждем 10 секунд
                    sleep 10
                    // Убиваем все процессы node.exe, если остались
                    bat 'taskkill /im node.exe /f || exit 0'
                }
            }
        }
    }

    post {
        success {
            script {
                def rawMessage = "✅ Сборка #${env.BUILD_NUMBER} успешна!\nПроект: ${env.PROJECT_NAME}\nДетали: ${env.BUILD_URL}"
                def encodedMessage = java.net.URLEncoder.encode(rawMessage, "UTF-8")
                bat """
                curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" -d "chat_id=${env.TELEGRAM_CHAT_ID}&text=${encodedMessage}&parse_mode=Markdown"
                """
            }
        }

        failure {
            script {
                def rawMessage = "❌ Сборка #${env.BUILD_NUMBER} завершена с ошибкой.\nПроект: ${env.PROJECT_NAME}\nЛоги: ${env.BUILD_URL}"
                def encodedMessage = java.net.URLEncoder.encode(rawMessage, "UTF-8")
                bat """
                curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" -d "chat_id=${env.TELEGRAM_CHAT_ID}&text=${encodedMessage}&parse_mode=Markdown"
                """
            }
        }
    }
}
