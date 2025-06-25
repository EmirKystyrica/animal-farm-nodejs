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
                bat 'no test'
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
            
            // Отправка email в любом случае
            emailext (
                to: 'vitabrevislongo@gmail.com',
                subject: "Jenkins Build ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    Статус сборки: ${currentBuild.currentResult}
                    Проект: ${env.JOB_NAME}
                    Номер сборки: ${env.BUILD_NUMBER}
                    Ссылка: ${env.BUILD_URL}
                    
                    Детали:
                    ${currentBuild.changeSets}
                    
                    Логи доступны по ссылке: ${env.BUILD_URL}consoleText
                """,
                attachLog: true
            )
        }
        success {
            echo "Сборка успешно завершена!"
            
            // Дополнительное письмо при успехе (опционально)
            emailext (
                to: 'vitabrevislongo@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} Deployment Completed",
                body: "Приложение успешно развернуто на порту 3001"
            )
        }
        failure {
            echo "Сборка провалена!"
            
            // Дополнительное письмо при ошибке
            emailext (
                to: 'vitabrevislongo@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} Build #${env.BUILD_NUMBER}",
                body: """
                    Сборка провалена!
                    Пожалуйста, проверьте логи:
                    ${env.BUILD_URL}consoleText
                    
                    Последние изменения:
                    ${currentBuild.changeSets}
                """,
                attachLog: true
            )
        }
    }
}
