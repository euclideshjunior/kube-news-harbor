pipeline {
    agent any

    stages {
        stage("Build Docker Image") {
            steps {
                script {
                    dockerapp = docker.build("euclideshjunior/kube-news:v${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage("Push Docker Image to Registry") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("v${env.BUILD_ID}")
                    }
                }
            }
        }

        stage("Deploy Kubernetes") {
            environment {
                tag_version = "v${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i s/{{TAG}}/$tag_version/g ./src/k8s/deployment.yml'
                    sh 'kubectl apply -f ./src/k8s/deployment.yml'
                }
            }
        }
    }
}
