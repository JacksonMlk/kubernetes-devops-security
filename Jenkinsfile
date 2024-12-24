pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded latert
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
                sh 'docker build -t segurox/numeric-app:${env.GIT_COMMIT} .'
                sh 'docker push segurox/numeric-app:${env.GIT_COMMIT}'

              }
              
            }
        }   
    }
}