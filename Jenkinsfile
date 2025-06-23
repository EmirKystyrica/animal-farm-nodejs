pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18' // Имя NodeJS из Jenkins Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                git credentialsId: 'github-token', url: 'https://github.com/EmirKystyrica/animal-farm-nodejs.git', branch: 'main'
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Installing dependencies...'
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                // Используем npx, чтобы cross-env заработал в Jenkins на Windows
                bat 'npx cross-env NODE_ENV=test PORT=3000 npm test'
            }
        }

        stage('Build / Run') {
            steps {
                echo 'Starting app...'
                // Чтобы не блокировать сборку, лучше запускать в фоне или через отдельный скрипт
                // Но для простоты запускаем так:
                bat 'npx cross-env PORT=3001 node app.js'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
            telegramSend(chatId: 292560946, message: "✅ Сборка прошла успешно: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
        failure {
            echo 'Pipeline failed!'
            telegramSend(chatId: 292560946, message: "❌ Сборка упала: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
    }
}
