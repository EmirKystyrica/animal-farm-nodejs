pipeline {
    agent any

    environment {
        TELEGRAM_BOT_TOKEN = '7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0'
        TELEGRAM_CHAT_ID = '292560946'
        PROJECT_NAME = 'animal-farm-nodejs'
        PORT_TEST = '3000'
        PORT_RUN = '3001'
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
                bat "npx cross-env NODE_ENV=test PORT=${PORT_TEST} npm test"
            }
        }

        stage('Deploy') {
            steps {
                script {
                    bat "start /B npx cross-env PORT=${PORT_RUN} node app.js"
                    sleep 10
                    // Завершаем запущенный node процесс, если нужно
                    bat 'taskkill /im node.exe /f || exit 0'
                }
            }
        }

        stage('Notify Success') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                script {
                    def message = "✅ Сборка #${env.BUILD_NUMBER} успешна!\nПроект: ${env.PROJECT_NAME}\nДетали: ${env.BUILD_URL}"
                    bat """
                    curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" ^
                         -d "chat_id=${env.TELEGRAM_CHAT_ID}" ^
                         -d "text=${message}" ^
                         -d "parse_mode=Markdown"
                    """
                }
            }
        }
    }

    post {
        failure {
            script {
                def message = "❌ Сборка #${env.BUILD_NUMBER} завершена с ошибкой.\nПроект: ${env.PROJECT_NAME}\nЛоги: ${env.BUILD_URL}"
                bat """
                curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" ^
                     -d "chat_id=${env.TELEGRAM_CHAT_ID}" ^
                     -d "text=${message}" ^
                     -d "parse_mode=Markdown"
                """
            }
        }
    }
}
