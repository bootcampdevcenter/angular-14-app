pipeline{

  agent {
    node {
      label 'node-nodejs'
    }
  }

  environment {
    registryCredential='docker-hub-credentials'
    registryFrontend = 'franaznarteralco/frontend-demo'
  }

  stages {
    stage('Build') {
      steps {
        sh 'npm install && npm run build'
      }
    }

    stage('Push Image to Docker Hub') {
      steps {
        script {
          dockerImage = docker.build registryFrontend + ":$BUILD_NUMBER"
          docker.withRegistry( '', registryCredential) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy to K8s') {
      steps{
        sh 'git clone https://github.com/dberenguerdevcenter/kubernetes-helm-docker-config.git configuracion --branch demo-java'
        sh 'kubectl apply -f configuracion/kubernetes-deployments/angular-14-app/deployment.yaml --kubeconfig=configuracion/kubernetes-config/config'
      }
    }
  }

  post {
    always {
      sh 'docker logout'
    }
  }
}
