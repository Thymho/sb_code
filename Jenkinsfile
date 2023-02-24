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
      dockerHubRegistry = 'rhcowls/sbimage'
      dockerHubRegistryCredential = 'docker_cre'
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
          // withDockerRegistry : docker pipeline 플러그인 설치시 사용가능.
          // dockerHubRegistryCredential : environment에서 선언한 docker_cre
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
      stage('Docker image push') {
        steps {
          withDockerRegistry(credentialsId: dockerHubRegistryCredential, url: '') {
            // withDockerRegistry : docker pipeline 플러그인 설치시 사용가능.
            // dockerHubRegistryCredential : environment에서 선언한 docker_cre  
            sh "docker push ${dockerHubRegistry}:${currentBuild.number}"
            sh "docker push ${dockerHubRegistry}:latest"
          }
        }
        post {
          failure {
            echo 'docker image push failure'
          }
          success {
            echo 'docker image push success'
          }
        }
      }
      stage('Docker container deploy') {
        steps {
          sh "docker rm -f sb"
          sh "docker run -dp 5656:8085 --name sb ${dockerHubRegistry}:${currentBuild.number}"
          }
        }
        post {
          failure {
            echo 'docker image push failure'
          }
          success {
            echo 'docker image push success'
          }
        }
      }
    }
}
