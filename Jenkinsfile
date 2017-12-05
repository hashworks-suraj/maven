pipeline {
  agent {
    docker {
      image 'maven:3-alpine'
      args '-v /root/.m2:/root/.m2'
    }
    
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        script {
          def server = Artifactory.newServer url: '127.0.0.1:8081', username: 'admin', password: 'password'
          def uploadSpec = """{
            "files": [
              {
                "pattern": "*.jar",
                "target": "example-repo-local/maven-build"
              }
            ]
          }"""
          server.upload(uploadSpec)
        }
        
      }
    }
    stage('test') {
      steps {
        sh 'mvn test'
      }
    }
  }
}