pipeline {
    agent any

    environment {
        IMAGE_NAME = 'local-registry:5000/rails-app:latest'
        KUBECONFIG = credentials('kubeconfig-credential-id')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Push to Local Registry') {
            steps {
                sh """
                docker push ${IMAGE_NAME}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl apply -f k8s/deployment.yml
                kubectl apply -f k8s/service.yml
                kubectl rollout status deployment/rails-app
                """
            }
        }
    }

    post {
        success {
            echo '✅ Build e deploy concluídos com sucesso!'
        }
        failure {
            echo '❌ Erro no pipeline!'
        }
    }
}
