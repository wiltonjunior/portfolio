
pipeline {
  agent any

  options {
    timeout(time: 30, unit: 'MINUTES')
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
  }

  parameters {
    string(name: 'BRANCH', defaultValue: 'master', description: 'Build branch.')
    string(name: 'HOST', defaultValue: 'wiltonjunior.dev', description: 'Build branch.')
    string(name: 'GIT_CREDENTIALS', defaultValue: "610a2666-682b-4e7f-91e9-b5630ff7bed2", description: 'Git Credentials')
  }

  environment {
    APPLICATION_NAME="portfolio"
    GIT_URL="git@github.com:wiltonjunior/web-portfolio.git"
    DOCKER_IMAGE="${env.APPLICATION_NAME}:${env.BUILD_NUMBER}" 
  }

  stages {
    stage('Git Checkout') {
      steps {
        checkout([$class: 'GitSCM',
          branches: [[name: "*/${params.BRANCH}"]],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          submoduleCfg: [],
          userRemoteConfigs: [[
            credentialsId: "610a2666-682b-4e7f-91e9-b5630ff7bed2",
            url: "${GIT_URL}"
          ]]
        ])
      }
    }

    stage('Build image web') {
      steps {
          sh """
              cp ./config/prd.env prd.env
              docker rm -f \$(docker ps --filter=name='${env.APPLICATION_NAME}*:*' -q ) || echo 'empty' 
              docker rmi -f \$(docker images --filter=reference='${env.APPLICATION_NAME}*:*' -q ) || echo 'empty'
              docker build -t ${env.DOCKER_IMAGE} .
            """
      }
    }

    stage('Start job') {
      steps {
            sh """
              docker run -d --restart always --name ${env.APPLICATION_NAME} -p 8081:80 ${env.DOCKER_IMAGE}
            """
      }
    }
  }
}
