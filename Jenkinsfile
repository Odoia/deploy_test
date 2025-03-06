pipeline {
    agent any

    environment {
        IMAGE_NAME = 'local-registry:5000/rails-app:latest'
        KUBECONFIG_CONTENT = credentials('kubeconfig-credential-id')
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
                docker push ${IMAGE_NAME}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                # Salva o kubeconfig no workspace para o kubectl usar
                echo "${KUBECONFIG_CONTENT}" > /tmp/kubeconfig
                export KUBECONFIG=/tmp/kubeconfig

                # Aplica os manifests
                kubectl apply -f k8s/deployment.yml
                kubectl apply -f k8s/service.yml
                kubectl rollout status deployment/rails-app
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build e deploy concluídos com sucesso!"
        }
        failure {
            echo "❌ Falha no pipeline!"
        }
    }
}
