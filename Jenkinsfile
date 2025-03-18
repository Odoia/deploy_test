pipeline {
    agent any

    environment {
        REPO_NAME = "deploy_test"
        IMAGE_NAME = "deploy_test"
        IMAGE_TAG = "latest"
        DOCKER_REGISTRY = "10.0.0.211:5000" // Substitua pelo IP do seu registry privado se necessário
        KUBECONFIG_PATH = "/etc/rancher/k3s/k3s.yaml" // Caminho do kubeconfig
        DEPLOYMENT_NAME = "deploy-test"
        NAMESPACE = "default" // Altere se estiver usando outro namespace
    }

    stages {
        stage('Checkout Código') {
            steps {
                git branch: 'main', url: 'https://github.com/Odoia/deploy_test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG ."
                }
            }
        }

        stage('Push para Docker Registry') {
            steps {
                script {
                    sh "docker push $DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG"
                }
            }
        }

        stage('Deploy no K3s') {
            steps {
                script {
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH set image deployment/$DEPLOYMENT_NAME $DEPLOYMENT_NAME=$DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG -n $NAMESPACE"
                    sh "kubectl --kubeconfig=$KUBECONFIG_PATH rollout status deployment/$DEPLOYMENT_NAME -n $NAMESPACE"
                }
            }
        }

        stage('Limpeza de Imagens Locais') {
            steps {
                script {
                    sh "docker rmi $DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG || true"
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
