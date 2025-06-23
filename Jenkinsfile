pipeline {
    agent any
    
    environment {
        TELEGRAM_TOKEN = "7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0"
        TELEGRAM_CHAT_ID = "292560946"
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
        
        stage('Notify') {
            steps {
                script {
                    sendTelegramNotification(true)
                }
            }
        }
    }
    
    post {
        failure {
            script {
                sendTelegramNotification(false)
            }
        }
    }
}

def sendTelegramNotification(Boolean isSuccess) {
    def message = isSuccess ? 
        """✅ Сборка #${env.BUILD_NUMBER} успешна!
📁 Проект: animal-farm-nodejs
🔗 Детали: ${env.BUILD_URL}""" : 
        """❌ Сборка #${env.BUILD_NUMBER} упала!
📁 Проект: animal-farm-nodejs
🔍 Логи: ${env.BUILD_URL}console"""
    
    // Кодируем параметры отдельно для надежности
    def encodedMessage = URLEncoder.encode(message, "UTF-8")
    def encodedChatId = URLEncoder.encode(env.TELEGRAM_CHAT_ID, "UTF-8")
    
    bat """
        curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_TOKEN}/sendMessage" ^
            -d "chat_id=${encodedChatId}" ^
            -d "text=${encodedMessage}" ^
            -d "parse_mode=Markdown"
    """
}
