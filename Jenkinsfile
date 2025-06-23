pipeline {
    agent any
    
    environment {
        TELEGRAM_TOKEN = "7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0"
        TELEGRAM_CHAT_ID = "292560946"  // Ваш chat ID
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
                    bat 'start /B npx cross-env PORT=3001 node app.js'
                    sleep(time: 10, unit: 'SECONDS')
                    bat 'taskkill /im node.exe /f'
                }
            }
        }
        
        stage('Notify Success') {
            steps {
                script {
                    sendTelegramMessage("✅ Сборка #${env.BUILD_NUMBER} успешна!")
                }
            }
        }
    }
    
    post {
        failure {
            script {
                sendTelegramMessage("❌ Сборка #${env.BUILD_NUMBER} упала!")
            }
        }
    }
}

def sendTelegramMessage(String text) {
    def fullMessage = "${text}\n" +
                     "📁 Проект: animal-farm-nodejs\n" +
                     "🔗 Подробности: ${env.BUILD_URL}"
    
    def telegramUrl = "https://api.telegram.org/bot${env.TELEGRAM_TOKEN}/sendMessage"
    def payload = [
        'chat_id': env.TELEGRAM_CHAT_ID,
        'text': fullMessage,
        'parse_mode': 'Markdown'
    ].collect { k, v -> "$k=${URLEncoder.encode(v
