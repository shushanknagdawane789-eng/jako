pipeline {
    agent any

    environment {
        DOCKER_USER = "shushankbittu"      // Docker Hub username
        IMAGE_NAME  = "timeless"           // Docker image name
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
                    java -version
                    mvn -version
                    docker --version
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKER_USER}/${IMAGE_NAME}:latest
                """
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
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    docker push ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
                    docker push ${DOCKER_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Update K8s Deployment') {
            steps {
                sh """
                    sed -i 's|image:.*|image: ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER}|g' k8s/deployment.yaml
                    cat k8s/deployment.yaml
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}