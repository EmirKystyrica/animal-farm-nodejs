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
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'npx cross-env NODE_ENV=test PORT=3000 npm test'
            }
        }

        stage('Build / Run') {
            steps {
                echo 'Starting app, waiting 10 seconds and killing process...'
                bat '''
                    start /B npx cross-env PORT=3001 node app.js
                    timeout /t 10 /nobreak
                    taskkill /im node.exe /f
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
            telegramSend botToken: '7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0',
                         chatId: '292560946',
                         message: "✅ Сборка прошла успешно: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        failure {
            echo 'Pipeline failed!'
            telegramSend botToken: '7794210694:AAE7oZscUYib7fK7sVw2JGS3OlXfwdfRNx0',
                         chatId: '292560946',
                         message: "❌ Сборка упала: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
