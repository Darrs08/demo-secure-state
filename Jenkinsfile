pipeline {
  agent any
  
  stages{
    stage ('Preps') {
      steps {
        slackSend color: "good", message: "Status: DEPLOYING CLOUD INFRA | Job: ${env.JOB_NAME} | Build number ${env.BUILD_NUMBER}"
        git 'https://github.com/Darrs08/demo-secure-state.git'
      }
    }   
  }
}
