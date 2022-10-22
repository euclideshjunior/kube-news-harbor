pipeline {
    agent any

    stages {
        stage("Build Docker Image") {
            steps {
                script {
                    dockerapp = docker.build("euclideshjunior/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage("Push Docker Image to Registry") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub')
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                }
            }
        }
    }
}