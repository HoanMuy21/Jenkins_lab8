pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'flask-app'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        DOCKER_USERNAME = 'vhmuy' // 👈 sửa thành Docker Hub username của bạn
        DOCKER_REGISTRY = "${DOCKER_USERNAME}/${DOCKER_IMAGE_NAME}"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // 👈 ID credentials tạo trong Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}:${DOCKER_TAG}")
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def container = docker.image("${DOCKER_REGISTRY}:${DOCKER_TAG}").run('-p 5000:5000')
                    try {
                        sh 'sleep 5'
                        sh 'curl --fail http://localhost:5000'
                    } finally {
                        container.stop()
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_REGISTRY}:${DOCKER_TAG}").push()
                        docker.image("${DOCKER_REGISTRY}:${DOCKER_TAG}").push('latest')
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
