pipeline {
    agent any
    
    environment {
        TELEGRAM_TOKEN = "7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0"
        TELEGRAM_CHAT_ID = "292560946"
        JENKINS_URL = "http://localhost:8080"
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
                    def message = """
                    ✅ Сборка #${env.BUILD_NUMBER} успешна!
                    Проект: animal-farm-nodejs
                    Детали: ${env.JENKINS_URL}/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/
                    """.stripIndent().trim()
                    
                    bat """
                        curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_TOKEN}/sendMessage" ^
                            -d "chat_id=${env.TELEGRAM_CHAT_ID}" ^
                            -d "text=${message}" ^
                            -d "parse_mode=Markdown"
                    """
                }
            }
        }
    }
    
    post {
        always {
            script {
                def status = currentBuild.result ?: 'SUCCESS'
                def message = """
                ${status == 'SUCCESS' ? '✅' : '❌'} Сборка #${env.BUILD_NUMBER} завершена
                Статус: ${status}
                Проект: animal-farm-nodejs
                Логи: ${env.JENKINS_URL}/job/${env.JOB_NAME}/${env.BUILD_NUMBER}/console
                """.stripIndent().trim()
                
                bat """
                    curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_TOKEN}/sendMessage" ^
                        -d "chat_id=${env.TELEGRAM_CHAT_ID}" ^
                        -d "text=${message}" ^
                        -d "parse_mode=Markdown"
                """
            }
        }
    }
}
