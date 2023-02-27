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
            sh "docker image rm -f ${dockerHubRegistry}:${currentBuild.number}"
            sh "docker image rm -f ${dockerHubRegistry}:latest"
          }
          success {
            echo 'docker image push success'
            sh "docker image rm -f ${dockerHubRegistry}:${currentBuild.number}"
            sh "docker image rm -f ${dockerHubRegistry}:latest"
          }
        }
      }
      stage('Docker container deploy') {
        steps {
          sh "docker rm -f sb"
          sh "docker run -dp 5656:8085 --name sb ${dockerHubRegistry}:${currentBuild.number}"
          }
        post {
          failure {
            echo 'docker container deployment failure'
            slackSend (color: '#FF0000', message: "FAILURE: docker container deployment '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          }
          success {
            echo 'docker container deployment success'
            slackSend (color: '#0000FF', message: "SUCCESS: docker container deployment '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          }
        }
      }
      stage('k8s manifest file update') {
        steps {
          git credentialsId: githubCredential,
              url: gitWebaddress,
              branch: 'main'
          
          // 이미지 태그 변경 후 메인 브랜치에 푸시
          sh "git config --global user.email ${gitEmail}"
          sh "git config --global user.name ${gitName}"
          sh "sed -i 's@${dockerHubRegistry}:.*@${dockerHubRegistry}:${currentBuild.number}@g' deploy/sb-deploy.yml"
          sh "git add ."
          sh "git commit -m 'fix:${dockerHubRegistry} ${currentBuild.number} image versioning'"
          sh "git branch -M main"
          sh "git remote remove origin"
          sh "git remote add origin ${gitSshaddress}"
          sh "git push -u origin main"

        }
        post {
          failure {
            echo 'Container Deploy failure'
          }
          success {
            echo 'Container Deploy success'  
          }
        }
      }
    }
}
