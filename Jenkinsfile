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

        stage('Notify Success') {
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
    def status = isSuccess ? "Сборка УСПЕШНА" : "Сборка УПАЛА"
    def text = "${status} | Проект: animal-farm-nodejs | Сборка #${env.BUILD_NUMBER} | Jenkins: ${env.BUILD_URL}"

    // Убираем кавычки и emoji, отправляем одним curl-запросом
    bat """
        curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_TOKEN}/sendMessage" ^
             -d chat_id=${env.TELEGRAM_CHAT_ID} ^
             -d text="${text}"
    """
}
