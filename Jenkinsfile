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
              docker.withRegistry('', "docker-hub") {
                sh 'docker build -t segurox/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push segurox/numeric-app:""$GIT_COMMIT""'
              }
              
            }
        }   
    }
}