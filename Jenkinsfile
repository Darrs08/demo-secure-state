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
        //sh "install wget zip python-pip -y"
        sh "curl -o terraform.zip https://releases.hashicorp.com/terraform/1.0.9/terraform_1.0.9_windows_amd64.zip"
        sh "unzip terraform.zip"
        sh "mv terraform /usr/bin"
        sh "rm -rf terraform.zip"
      }     
    }
    stage('Apply') {
      environment {
        TF_VAR_option_5_aws_ssh_key_name = "adminKey"
        TF_VAR_option_6_aws_ssh_key_name = "adminKey"
        AWS_ACCESS_KEY_ID=credentials('ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY=credentials('SECRET_KEY')
      }
      steps {
        bat "ls"
        bat "cd fitcycle_terraform"
        bat "ls"
        sh "terraform init --backend-config=bucket=BUCKET_NAME --backend-config=key=terraform.tfstate --backend-config=region=us-east-1 -lockfile=false"
        sh "terraform apply --input=false --var-file=example_vars_files/us_west_1_mysql.tfvars --auto-approve"
        sh "cd fitcycle_terraform && terraform output --json > Terraform_Output.json"
      }
    }
  }
  post {
        success {
            slackSend color: "good", message: "Status: PIPELINE ${currentBuild.result} | Job: ${env.JOB_NAME} | Build number ${env.BUILD_NUMBER}"
            archiveArtifacts artifacts: 'fitcycle_terraform/Terraform_Output.json', fingerprint: true
            archiveArtifacts artifacts: 'violations_using_api.py', fingerprint: true
            echo 'success'
        }
        failure {
            slackSend color: "danger", message: "Status: PIPELINE ${currentBuild.result} | Job: ${env.JOB_NAME} | Build number ${env.BUILD_NUMBER}"
            echo 'failed'
        }
        aborted {
            slackSend color: "warning", message: "Status: PIPELINE ${currentBuild.result} | Job: ${env.JOB_NAME} | Build number ${env.BUILD_NUMBER}"
            echo 'aborted'
        }
    }
}

