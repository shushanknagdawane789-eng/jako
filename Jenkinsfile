pipeline {
    agent any

    environment {
        DOCKER_REPO = "shushanknagdawane789"
        DOCKER_USER = "node-app"
        IMAGE_NAME = "node-app"
        CONTAINER_NAME = "node-container"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shushanknagdawane789-eng/timeless.git'
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                echo "Node Version:"
                node -v

                echo "NPM Version:"
                npm -v

                echo "Docker Version:"
                docker --version
                '''
            }
        }

        stage('Debug') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh 'find . -name package.json'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('timeless') {
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('timeless') {
                    sh 'npm test'
                }
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${DOCKER_REPO}:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                docker tag ${DOCKER_REPO}:${BUILD_NUMBER} ${DOCKER_REPO}/${DOCKER_USER}:${BUILD_NUMBER}
                docker push ${DOCKER_REPO}/${DOCKER_USER}:${BUILD_NUMBER}
                '''
            }
        }

        stage('K8s-Deployment') {
            steps {
                sh '''
               sed -i "s|shushanknagdawane789/node-app:.*|shushanknagdawane789/node-app:${BUILD_NUMBER}|g" k8s/deployment.yaml
                '''
                sh 'cat k8s/deployment.yaml'
            }
        }
    }
}