pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-ecommerce-backend"
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGISTRY = "kirthiksubbiah/my-ecommerce"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/kirthiksubbiah/ecommerce-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-credentials', url: '') {
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}"
                    sh "docker push ${REGISTRY}:${DOCKER_TAG}"
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    sh "docker stop ecommerce || true"
                    sh "docker rm ecommerce || true"
                    sh "docker run -d --name ecommerce -p 5000:5000 ${REGISTRY}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
