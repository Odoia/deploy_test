pipeline {
  agent any

  environment {
    IMAGE_NAME = "deploy_test"
    REGISTRY = "localhost:5000" // se usar um registry local, pode ajustar
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/Odoia/deploy_test.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh 'docker build -t $IMAGE_NAME .'
        }
      }
    }

    stage('Deploy') {
      steps {
        script {
          // Remove e sobe novamente no Swarm
          sh '''
            docker service rm deploy_test || true
            docker service create \
              --name deploy_test \
              --replicas 1 \
              --network internal_network \
              -p 3000:3000 \
              deploy_test
          '''
        }
      }
    }
  }
}
