pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/shushanknagdawane789-eng/jako.git'
            }
        }

        stage('Terraform Init') {
    steps {
        dir('timeless') {
            sh 'pwd'
            sh 'ls -la'
            sh 'find . -name "*.tf"'
            sh 'terraform init'
        }
    }
}

stage('Terraform Validate') {
    steps {
        dir('timeless') {
            sh 'terraform validate'
        }
    }
}

stage('Terraform Plan') {
    steps {
        dir('timeless') {
            sh 'terraform plan -out=tfplan'
        }
    }
}

stage('Terraform Apply') {
    steps {
        dir('timeless') {
            sh 'terraform apply -auto-approve tfplan'
        }
    }
}
