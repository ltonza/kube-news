pipeline {
    agent any

    environment {
        IMAGE_NAME = 'ltonza/kube-news'
        TAG_VERSION = "v${env.BUILD_ID}"
    }

    stages {
        stage ('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${TAG_VERSION}", '-f ./src/Dockerfile ./src')
                }
            }
        }
        
        stage ('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        
        stage ('Deploy Kubernetes') {
            steps {
                withKubeConfig ([credentialsId: 'kubeconfig']){
                    sh 'echo "$IMAGE_NAME:$TAG_VERSION"'
                    sh 'sed -i "s#\($IMAGE_NAME\):.*#\1:$TAG_VERSION#g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }
    }
}
