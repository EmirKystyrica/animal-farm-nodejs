pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18'
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
            telegramSend message: "✅ Сборка прошла успешно: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        failure {
            echo 'Pipeline failed!'
            telegramSend message: "❌ Сборка упала: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
    }
}
