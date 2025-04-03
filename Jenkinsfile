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
                    sh "docker stop cricket-captains || true"
                    sh "docker rm cricket-captains || true"
                    sh "docker run -d --name cricket-captains -p 882:80 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Clean workspace after build
        }
        success {
            echo "Website deployed successfully at http://${EC2_PUBLIC_IP}"
        }
    }
}
                        
