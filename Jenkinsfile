
pipeline {
    agent any

    tools {
         terraform 'Terraform'
     }

    environment {
        //Credentials for Prod environment
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID') 
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    stages {
        stage('Git checkout from prod branch') {
            steps {
                echo 'Cloning project codebase...'
                git branch: 'main', url: 'https://github.com/HILL-TOPCONSULTANCY/jenkins-cicd-repo.git'
                sh 'ls'
            }
        }
        

         stage('Verify Terraform Version') {
            steps {
                echo 'verifying the terrform version...'
                sh 'terraform --version'
               
            }
        }
        
        stage('Terraform init') {
            steps {
                echo 'Initiliazing terraform project...'
                sh 'terraform init'
               
            }
        }
        
        stage('Terraform validate') {
            steps {
                echo 'Code syntax checking...'
                sh 'terraform validate'
                sh 'pwd'
            }
        }
        
        stage('Terraform plan') {
            steps {
                echo 'Terraform plan for the dry run...'
                sh 'terraform plan -var-file="./prod-values.tfvars"'
               
            }
        }
        
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(id: 'Proceed1', message: 'Approve Terraform Apply?', parameters: [
                        [$class: 'TextParameterDefinition', defaultValue: 'Yes', description: 'Type Yes to approve', name: 'Approval']
                    ])
                    if (userInput['Approval'] != 'Yes') {
                        error "Pipeline aborted by user"
                    }
                }
            }
        }
        
        stage('Terraform apply') {
            steps {
                echo 'Terraform apply...'
                sh 'terraform apply -var-file="./prod-values.tfvars" --auto-approve'
            } 
        }

        stage('Manual approval to Delete') {
            steps {
                
                input 'Approval required to clean environment'
               
            }
        }
        
        stage('Terraform Destroy') {
            steps {
                echo 'Terraform Destroy...'
                sh 'terraform destroy -var-file="./prod-values.tfvars" --auto-approve'
                }
            }
    }

}
