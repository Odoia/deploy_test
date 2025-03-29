pipeline {
  agent any

  environment {
    IMAGE_NAME = "deploy_test"
    REGISTRY = "10.0.0.211:5000"
    TAG = "latest"
  }

  stages {
    stage('Clonar projeto') {
      steps {
        git url: 'https://github.com/Odoia/deploy_test.git', branch: 'main'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh 'docker build -t $REGISTRY/$IMAGE_NAME:$TAG .'
        }
      }
    }

    stage('Push to Local Registry') {
      steps {
        sh 'docker push $REGISTRY/$IMAGE_NAME:$TAG'
      }
    }

    stage('Deploy no Swarm') {
      steps {
        sh 'docker service update --image 10.0.0.211:5000/deploy_test:latest deploy_test_web'
      }
    }
  }

  post {
    success {
      echo "Imagem enviada com sucesso para ${REGISTRY}/${IMAGE_NAME}:${TAG}"
    }
    failure {
      echo "Erro no processo de build ou push"
    }
  }
}
