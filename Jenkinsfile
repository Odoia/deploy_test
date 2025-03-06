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
                sh '''
                echo "${KUBECONFIG_CONTENT}" > /tmp/kubeconfig
                /kaniko/executor \
                  --context `pwd` \
                  --dockerfile Dockerfile \
                  --destination local-registry:5000/rails-app:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                export KUBECONFIG=/tmp/kubeconfig
                kubectl apply -f k8s/deployment.yml
                kubectl apply -f k8s/service.yml
                kubectl rollout status deployment/rails-app
                '''
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
