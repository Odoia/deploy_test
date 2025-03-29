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
          docker.build("${REGISTRY}/${IMAGE_NAME}:${TAG}")
        }
      }
    }

    stage('Push to Local Registry') {
      steps {
        script {
          docker.withRegistry("http://${REGISTRY}") {
            docker.image("${REGISTRY}/${IMAGE_NAME}:${TAG}").push()
          }
        }
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
