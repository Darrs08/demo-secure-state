pipeline {
  agent any
  tools {
        terraform 'terraform1.0.9'
  }
  stages{
    stage ('Preparations') {
      steps {
        slackSend color: "good", message: "Status: DEPLOYING CLOUD INFRA | Job: ${env.JOB_NAME} | Build number ${env.BUILD_NUMBER}"
        git 'https://github.com/Darrs08/demo-secure-state.git'
      }
    }
    stage('Install TF Dependencies') {
      steps{
        sh '''terraform init'''
      }     
    }
  }
}
