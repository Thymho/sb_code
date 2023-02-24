pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools {
    maven 'my_maven'
  }
  stages {
    stage('Example') {
      steps {
        sh 'mvn clean install'
        echo 'Hello World'
        }
    }
  }
}
