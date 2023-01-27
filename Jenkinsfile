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
                    //Deploy do banco de dados
                    sh 'kubectl apply -f ./k8s/deployment-db.yaml'
                    //Atualiza a tag da imagem
                    sh 'sed -i "s#$IMAGE_NAME:.*#$IMAGE_NAME:$TAG_VERSION#g" ./k8s/deployment.yaml'
                    //Deploy da nova imagem da aplicação
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }
    }
}
