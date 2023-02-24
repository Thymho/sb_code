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
      gitCredential = 'rhcowls' // github credential 생성시의 ID
      dockerHubRegistry = 'rhcowls/sbimage'
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
          sh 'mvn clean install'
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
      stage('Docker image Build') {
        steps {
          sh "docker image build -t ${dockerHubRegistry}:${currentBuild.number} ."
          sh "docker image build -t ${dockerHubRegistry}:latest ."
        }
        post {
          failure {
            echo 'docker image build failure'
          }
          success {
            echo 'docker image build success'
          }
        }
      }
    }
}
