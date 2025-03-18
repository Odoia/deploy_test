pipeline {
    agent any

    environment {
        JOB_NAME = "docker-builder"
        KUBECONFIG_PATH = "/etc/rancher/k3s/k3s.yaml"
        IMAGE_NAME = "10.0.0.211:5000/deploy_test:latest"
    }

    stages {
        stage('Checkout Código') {
            steps {
                git branch: 'main', url: 'https://github.com/Odoia/deploy_test.git'
            }
        }

        stage('Criar Job de Build no K3s') {
            steps {
                script {
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH delete job --ignore-not-found=true $JOB_NAME"
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH apply -f k8s-builder.yaml"
                }
            }
        }

        stage('Aguardar Build da Imagem') {
            steps {
                script {
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH wait --for=condition=complete --timeout=600s job/$JOB_NAME"
                }
            }
        }

        stage('Deploy no K3s') {
            steps {
                script {
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH apply -f k8s-deployment.yaml"
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH rollout status deployment/deploy-test"
                }
            }
        }
    }

    post {
        success {
            echo "Deploy concluído com sucesso!"
        }
        failure {
            echo "Erro no deploy!"
        }
    }
}
