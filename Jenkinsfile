pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools {
    maven 'my_maven'
  }
  
  environment {
    gitName = 'Thymho'
    gitEmail = 'rhcowls@naver.com'
    gitWebaddress = 'https://github.com/Thymho/sb_code.git'
    gitSshaddress = 'git@github.com:Thymho/sb_code.git'
    gitCredential = 'git_cre'
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
