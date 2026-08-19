pipeline {
    agent any

    environment {
        REGISTRY = 'sagarnehra47'
        IMAGE_NAME = 'trend-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ${REGISTRY}/${IMAGE_NAME}:latest"
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh "docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${REGISTRY}/${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh "kubectl apply -f deployment.yaml --validate=false"
            }
        }
    }
}
