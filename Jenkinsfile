pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools {
    maven 'my_maven'
  }
  
  environment {
    gitName = 'Thymho'
    gitEmail = 'rhcowls@naver.com'
    gitWebaddress=''
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
