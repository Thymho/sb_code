pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools {
    maven 'my_maven'
  }
  
  environment {
    gitName = 'Thymho'
    gitEmail = 'rhcowls@naver.com'
		// github의 http 주소
    gitWebaddress = 'https://github.com/Thymho/sb_code.git'
		// github의 ssh 주소
    gitSshaddress = 'git@github.com:Thymho/sb_code.git'
    gitCredential = 'git_cre' // github credential 생성시의 ID
  }
  
  stages {
    stage('checkout Github') {
      steps {
	checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: gitCredential, url: gitWebaddress]]])
      }
      post {
	failure {
	  echo 'Repository clone failure'
	}
	success {
	  echo 'Repository clone success'
	}
	}
    }
     stage('maven Build') {
      steps {
        sh 'mvm clean install'
      }
       post {
	failure {
	  echo 'maven build failure'
	}
	success {
	  echo 'maven build success'
	}
      }
    }
  }
}
