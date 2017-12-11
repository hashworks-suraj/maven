pipeline {
  agent {
    docker {
      image 'maven:3-alpine'
      args '-v /root/.m2:/root/.m2'
    }
    
  }
  stages {
    stage('Build') {
      parallel {
        stage('Build') {
          steps {
            sh 'mvn -B -DskipTests clean package'
            script {
              def server = Artifactory.newServer url: 'http://artifactory:8081/artifactory', username: 'admin', password: 'password'
              def uploadSpec = """{
                "files": [
                  {
                    "pattern": "*.jar",
                    "target": "generic-local/"
                  }
                ]
              }"""
              server.upload(uploadSpec)
            }
            
          }
        }
        stage('fetch') {
          steps {
            script {
              def server = Artifactory.newServer url: 'artifactory:8081/artifactory', username: 'admin', password: 'password'
              def downloadSpec = """{
                "files": [
                  {
                    "pattern": "generic-local/*.jar",
                    "target": "files/"
                  }
                ]
              }"""
              server.download(downloadSpec)
            }
            
          }
        }
      }
    }
    stage('test') {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          
        }
        
      }
    }
  }
}