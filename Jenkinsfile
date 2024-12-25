pipeline {
  agent any

  stages {
    stage('Build Artifact') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar' //so that they can be downloaded later
      }
    }
    stage('Unit Test') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }
    stage('Docker Build and Push') {
      steps {
        sh 'printenv'
        withDockerRegistry([credentialsId: "docker-hub", url:""]) {
          sh 'docker build -t segurox/numeric-app:${GIT_COMMIT} .'
          sh 'docker push segurox/numeric-app:${GIT_COMMIT}'
        }
      }
    }
    stage('Kubernetes Deployment - DEV') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
          // Corrigido a interpolação de variáveis no sed
          sh "sed -i 's#<aceidssidharith67/numeric-app>:<GIT_COMMIT>#aceidssidharith67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }
  }
}
