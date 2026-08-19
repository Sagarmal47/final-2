pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "<your-dockerhub-username>/trend-app:${BUILD_NUMBER}"
        DOCKER_LATEST = "<your-dockerhub-username>/trend-app:latest"
        AWS_REGION = "us-east-1"
        EKS_CLUSTER = "trend-eks-cluster"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Vennilavanguvi/Trend.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} -t ${DOCKER_LATEST} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                    sh "docker push ${DOCKER_LATEST}"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh "aws eks update-kubeconfig --region ${AWS_REGION} --name ${EKS_CLUSTER}"
                sh "sed -i 's|<your-dockerhub-username>/trend-app:latest|'${DOCKER_IMAGE}'|g' deployment.yaml"
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
