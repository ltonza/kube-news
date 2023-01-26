pipeline {
    agent any
    stages {
        stage ('Build Docker Image') {
            steps {
                script {
                    //dockerapp = docker.build("ltonza/kube-news:v${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        def dockerImage = docker.build("ltonza/kube-news:v${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                        dockerImage.push()
                    }
                }
            }
        }
        /*
        stage ('Push Docker Image') {
            steps {
                script {
                    docker.withRegitry('https://registry.hub.docker.com', 'dockerhub'){
                        dockerapp.push('latest')
                        dockerapp.push("v${env.BUILD_ID}")
                    }
                }
            }
        }
        */
    }
}
