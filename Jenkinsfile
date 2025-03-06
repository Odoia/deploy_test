pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: kaniko
spec:
  containers:
    - name: kaniko
      image: gcr.io/kaniko-project/executor:debug
      command: ["/busybox/cat"]
      tty: true
      volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: kaniko-secret
"""
        }
    }

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

        stage('Build with Kaniko') {
            steps {
                sh '''
                echo "${KUBECONFIG_CONTENT}" > /tmp/kubeconfig
                /kaniko/executor \
                  --dockerfile Dockerfile \
                  --context /workspace \
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
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executado com sucesso!"
        }
        failure {
            echo "❌ Pipeline falhou!"
        }
    }
}
