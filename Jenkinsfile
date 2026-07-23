pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/shushanknagdawane789-eng/jako.git'
            }
        }
         stage('Debug') {
    steps {
        sh 'pwd'
        sh 'find . -name "*.tf"'
        sh 'ls -R'
    }
}
        stage('Terraform Init') {
            steps {
                dir('jako') {
                    sh 'pwd'
                    sh 'find . -name "*.tf"'
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                dir('jako') {
                    sh 'terraform validate'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('jako') {
                    sh 'terraform plan -out=tfplan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('jako') {
                    sh 'terraform apply -auto-approve tfplan'
                }
            }
        }

    }
}
