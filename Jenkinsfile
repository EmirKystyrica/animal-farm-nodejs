pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18' // Убедись, что в Jenkins Global Tool Configuration есть NodeJS с таким именем
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/EmirKystyrica/animal-farm-nodejs.git', branch: 'main'
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test' // если нет тестов, закомментируй
            }
        }

        stage('Build / Run') {
            steps {
                echo 'Starting app...'
                sh 'npm start'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
            // В Telegram Bot Plugin **нет поля для chatId в настройках Jenkins** — поэтому
            // нужно указывать chatId напрямую в скрипте, чтобы бот знал, куда слать уведомления
            telegramSend(chatId: '292560946', message: "✅ Сборка прошла успешно: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
        failure {
            echo 'Pipeline failed!'
            telegramSend(chatId: '292560946', message: "❌ Сборка упала: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
    }
}
