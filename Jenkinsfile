pipeline {
    agent any

    environment {
       DOCKER_USER  = "shushankbittu"
        IMAGE_NAME   = "timeless"
        DOCKER_REPO  = "shushankbittu"

        CLUSTER_NAME = "zomato"
        REGION       = "ap-southeast-2"
    }

    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'docker-hub-creds',
                    url: 'https://github.com/shushanknagdawane789-eng/jako.git'
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                echo "Java Version:"
                java -version

                echo "Maven Version:"
                mvn -version

                echo "Docker Version:"
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
                sh '''
                docker build -t ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} .
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
        docker tag ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKER_USER}/${IMAGE_NAME}:latest
        docker push ${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
        docker push ${DOCKER_USER}/${IMAGE_NAME}:latest
        '''
    }
}
            }
        }
    stage('Debug Workspace') {
    steps {
        sh '''
        echo "Current Directory:"
        pwd

        echo "Workspace Files:"
        ls -la

        echo "Searching deployment.yaml..."
        find . -name deployment.yaml

        echo "Listing k8s folder..."
        ls -la k8s || true
        '''
    }
}
        stage('Image-Name-change'){
                steps {
          
                   sh '''
sed -i "s|shushankbittu/timeless:latest|${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER}|g" k8s/deployment.yaml
'''
                    sh 'cat k8s/deployment.yaml'
                }
            }
            stage('Deploy to cluster'){
                steps{
                    withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_creds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                         sh '''
                    echo "Cluster: ${CLUSTER_NAME}"
echo "Region: ${REGION}"
                            aws eks update-kubeconfig --name ${CLUSTER_NAME} --region ${REGION}
                            
                            kubectl get nodes
                            kubectl apply -f k8s/deployment.yaml
                            kubectl apply -f k8s/service.yaml
                            kubectl get pods 
                            kubectl get deployment
                            kubectl get svc 
                         '''
                    }
                }
            }
        }
        post {

                success {
                    echo "Application deployed successfully to Amazon EKS."
                }

                failure {
                    echo "Pipeline failed. Please check the logs."
                }

                always {
                    cleanWs()
                }
            }

    }
