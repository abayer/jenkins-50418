pipeline {
  agent any
  stages {
    stage('Server build (Java 8)') {
      agent {
        docker {
          image 'openjdk:8'
          args '-v /srv/docker/jenkins-containers/cache/gradle-java8:/root/.gradle'
        }
      }
      steps {
        sh 'cd server && ./gradlew clean && ./gradlew build'
      }
    }
    stage('Client build') {
      agent {
        docker {
          image 'node:9'
          args '--network jenkinscontainers_default --link sonarqube -v /srv/docker/jenkins-containers/cache/npm-node9:/root/.npm'
        }
      }
      steps {
        withSonarQubeEnv('sonar') {
          sh 'cd ./client && npm install'
        }
      }
    }
    stage('Server SonarQube analysis') {
      agent {
        docker {
          image 'openjdk:8'
          args '--network jenkinscontainers_default --link sonarqube -v /srv/docker/jenkins-containers/cache/gradle-java8:/root/.gradle'
        }
      }
      steps {
        withSonarQubeEnv('sonar') {
          sh 'cd ./server && ./gradlew --info sonarqube'
        }
      }
    }
    stage('Server SonarQube QG') {
      agent {
        docker {
          image 'openjdk:8'
          args '--network jenkinscontainers_default --link sonarqube -v /srv/docker/jenkins-containers/cache/gradle-java8:/root/.gradle'
        }
      }
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate()
        }
      }
    }
    stage('Client SonarQube analysis') {
      agent {
        docker {
          image 'node:9'
          args '--network jenkinscontainers_default --link sonarqube -v /srv/docker/jenkins-containers/cache/npm-node9:/root/.npm'
        }
      }
      steps {
        withSonarQubeEnv('sonar') {
          sh 'cd ./client && npm install && npm run-script sonar-scanner'
        }
      }
    }
    stage('Client SonarQube QG') {
      agent {
        docker {
          image 'node:9'
          args '--network jenkinscontainers_default --link sonarqube -v /srv/docker/jenkins-containers/cache/npm-node9:/root/.npm'
        }
      }
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate()
        }
      }
    }
  }
}
