pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out Countdown application...'
                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo 'Running basic application checks...'
                sh 'test -f Dockerfile'
                sh 'test -f app/index.html'
                sh 'test -f app/style.css'
                sh 'test -f app/script.js'
                echo 'All required files are present.'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t countdown-app:latest .'
            }
        }

        stage('Build Complete') {
            steps {
                echo 'Countdown Docker image built successfully!'
            }
        }
    }
}