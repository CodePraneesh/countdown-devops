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
                echo 'Checking required files...'

                sh 'test -f Dockerfile'
                sh 'test -f app/index.html'
                sh 'test -f app/style.css'
                sh 'test -f app/script.js'
                sh 'test -f ansible/inventory.ini'
                sh 'test -f ansible/setup.yml'
                sh 'test -f terraform/main.tf'

                echo 'All required files are present.'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Countdown Docker image...'
                sh 'docker build -t countdown-app:latest .'
            }
        }

        stage('Ansible Configuration') {
            steps {
                echo 'Running Ansible configuration...'

                sh '''
                    ansible-playbook \
                    -i ansible/inventory.ini \
                    ansible/setup.yml
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                echo 'Checking Terraform infrastructure...'

                dir('terraform') {
                    sh 'terraform init -input=false'
                    sh 'terraform plan -input=false'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                echo 'Applying Terraform configuration...'

                dir('terraform') {
                    sh 'terraform apply -auto-approve -input=false'
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                echo 'Deploying Countdown application to Kubernetes...'

                sh '''
                    docker save countdown-app:latest -o countdown-app.tar
                    sudo k3s ctr images import countdown-app.tar
                    kubectl rollout restart deployment/countdown-app
                    kubectl rollout status deployment/countdown-app --timeout=120s
                '''
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying Kubernetes deployment...'

                sh 'kubectl get pods'
                sh 'kubectl get service countdown-service'

                echo 'Countdown application deployed successfully!'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the console output.'
        }
    }
}