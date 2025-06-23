pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18' // Проверь, что в Jenkins есть NodeJS с этим именем
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
                // Запускаем тесты с NODE_ENV=test и PORT=3000 для изоляции
                bat 'cross-env NODE_ENV=test PORT=3000 npm test'
            }
        }

        // Опциональный этап запуска сервера в фоне для интеграционных тестов
        // Закомментирован, чтобы пайплайн не висел
        /*
        stage('Run Server') {
            steps {
                echo 'Starting server in background...'
                // Windows: запускаем npm start в фоне
                bat 'start /B npm start'
                echo 'Waiting for server to start...'
                sleep(time: 10, unit: 'SECONDS')
                // Здесь можно добавить интеграционные тесты, запросы к API и т.д.
            }
            post {
                always {
                    echo 'Stopping server...'
                    // Убиваем node процессы (осторожно на общей машине)
                    bat 'taskkill /F /IM node.exe || echo "No node processes found"'
                }
            }
        }
        */
    }

    post {
        always {
            echo 'Pipeline finished.'
            cleanWs() // очищаем workspace после сборки
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
