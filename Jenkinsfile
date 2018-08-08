pipeline {
  agent any
  stages {
    stage('Server SonarQube analysis') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'echo in sonar env'
        }
      }
    }
  }
}
