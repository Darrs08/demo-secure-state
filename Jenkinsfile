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
    stage('Apply') {
      environment {
        TF_VAR_option_5_aws_ssh_key_name = "cloud_user"
        TF_VAR_option_6_aws_ssh_key_name = "cloud_user"
        AWS_ACCESS_KEY_ID= credentials('ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY= credentials('SECRET_KEY')
      }
      steps {
        sh "cd fitcycle_terraform/ && terraform init --backend-config=\"bucket=BUCKET_NAME\" --backend-config=\"key=terraform.tfstate\" --backend-config=\"region=us-east-1\" -lock=false && terraform apply --input=false --var-file=example_vars_files/us_west_1_mysql.tfvars --auto-approve"
        sh "cd fitcycle_terraform && terraform output --json > Terraform_Output.json"
      }
    }
  }
}

