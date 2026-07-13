pipeline {
    agent any

    stages {
        stage('Clone from SCM') {
            steps {
                echo 'Source code cloned from GitHub SCM'
            }
        }

        stage('Build') {
            steps {
                sh 'echo Build Successful'
            }
        }

        stage('Test') {
            steps {
                sh 'echo Test Successful'
            }
        }

        stage('Deploy') {
            steps {
                sh 'echo Deployment Successful'
            }
        }
    }
}