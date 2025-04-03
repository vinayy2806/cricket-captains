pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = "vinayy2806/cricket-captains"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        EC2_PUBLIC_IP = "34.207.234.31" // Replace with your EC2 public IP
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vinayy2806/cricket-captains.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push('latest')
                    }
                }
            }
        }
        stage('Deploy on EC2') {
            steps {
                script {
                    sh """
                        ssh -o StrictHostKeyChecking=no ec2-user@${34.207.234.31} '
                        docker stop cricket-captains || true &&
                        docker rm cricket-captains || true &&
                        docker run -d --name cricket-captains -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}'
                    """
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        success {
            echo "Website deployed successfully at http://${EC2_PUBLIC_IP}"
        }
    }
}
