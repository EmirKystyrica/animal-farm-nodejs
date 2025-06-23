pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18'
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token',
                    url: 'https://github.com/EmirKystyrica/animal-farm-nodejs.git',
                    branch: 'main'
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
            telegramSend message: "✅ Сборка прошла успешно: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }

        failure {
            echo 'Pipeline failed!'
            telegramSend message: "❌ Сборка упала: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
    }
}
