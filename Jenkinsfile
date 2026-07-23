pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/shushanknagdawane789-eng/jako.git'
            }
        }
        
        stage('Check Terraform Files') {
            steps {
                sh 'pwd'
                sh 'find . -name "*.tf"'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh 'find . -name "*.tf"'
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
    steps {
        dir('timeless') {
            sh 'pwd'
            sh 'ls -la'
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

}   
}   
