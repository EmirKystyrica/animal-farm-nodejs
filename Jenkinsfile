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
                    bat 'start /B npx cross-env PORT=3001 node app.js'
                    sleep time: 10, unit: 'SECONDS'
                    bat 'taskkill /im node.exe /f'
                }
            }
        }

        stage('Notify Success') {
            steps {
                powershell '''
                    $text = "✅ Сборка УСПЕШНА\\nПроект: ${env.PROJECT_NAME}\\nСборка #${env.BUILD_NUMBER}\\nJenkins: http://localhost:8080/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/"
                    $uri = "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage"
                    $payload = @{
                        chat_id = "${env.TELEGRAM_CHAT_ID}"
                        text = $text
                        parse_mode = "Markdown"
                    } | ConvertTo-Json -Depth 3
                    Invoke-RestMethod -Uri $uri -Method Post -Body $payload -ContentType "application/json"
                '''
            }
        }
    }

    post {
        failure {
            powershell '''
                $text = "❌ Сборка ПРОВАЛЕНА\\nПроект: ${env.PROJECT_NAME}\\nСборка #${env.BUILD_NUMBER}\\nJenkins: http://localhost:8080/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/"
                $uri = "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage"
                $payload = @{
                    chat_id = "${env.TELEGRAM_CHAT_ID}"
                    text = $text
                    parse_mode = "Markdown"
                } | ConvertTo-Json -Depth 3
                Invoke-RestMethod -Uri $uri -Method Post -Body $payload -ContentType "application/json"
            '''
        }
    }
}
