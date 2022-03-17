pipeline {
  environment{
  def getBrn = getGitBranchName()
  }
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
    stage ('Preparations') {
      steps {
        
        echo env.getBrn
        
      }
    }
    stage('Install TF Dependencies') {
      steps{
        //sh "install wget zip python-pip -y"
        sh "curl -o terraform.zip https://releases.hashicorp.com/terraform/1.0.9/terraform_1.0.9_linux_amd64.zip"
        sh "unzip terraform.zip"
        sh "mv terraform /usr/bin"
        sh "rm -rf terraform.zip"
      }     
    }
    stage('Create a Bucket and KeyPair') {
      steps {
        withAWS(region: "us-east-1", credentials: 'cloud_user') {
          awsIdentity()
          sh 'aws s3api create-bucket --bucket demodarren --region us-east-1'
          sh 'aws ec2 create-key-pair --key-name adminKey --key-type rsa'
        }
      }
    }
    stage ('Initialization Stage') {
      steps {
      sh 'echo Initialization started && sleep 180 && echo Initialization complete'
      }
    }
    stage('Apply') {
      environment {
        TF_VAR_option_5_aws_ssh_key_name = "adminKey"
        TF_VAR_option_6_aws_ssh_key_name = "adminKey"
        TF_VAR_option_1_aws_access_key = credentials('ACCESS_KEY_ID')
        TF_VAR_option_2_aws_secret_key = credentials('SECRET_KEY')
        AWS_ACCESS_KEY_ID= credentials('ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY= credentials('SECRET_KEY') 
      }
      steps {
        sh "cd fitcycle_terraform/ && terraform init -migrate-state --backend-config=bucket=demodarren --backend-config=key=path/to/my/key/some.tfstate --backend-config=region=us-east-1 -lockfile=false && terraform apply --input=false --var-file=example_vars_files/us_east_1_mysql.tfvars --auto-approve"
        sh "cd fitcycle_terraform && terraform output --json > Terraform_Output.json"
      }
      post {
        success {
          echo 'success'
        }
        failure {
          echo 'failed'
        }
        aborted {           
          echo 'aborted'
        }
      }
    }
  }
}
def getGitBranchName() {
    branch_name = scm.branches[0].name
	if (branch_name.contains("*/")) {
		branchName = branch_name.split("\\*/")[1]
    } else {
		branchName = branch_name.split("\\*/")[1]
	}
    return branchName
}
