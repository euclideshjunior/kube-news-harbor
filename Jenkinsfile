pipeline {
    agent any

    stages {
        stage("Build Docker Image") {
            environment {
                tag_version = "v${env.BUILD_ID}"
            }
            steps {
                script {
                    sh 'sed -i s/{{TAG_APP}}/$tag_version/g ./src/views/partial/nav-bar.ejs'
                    dockerapp = docker.build("registry.reitoria-edu.lab/esrrnp/kube-news:v${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage("Push Docker Image to Registry") {
            steps {
                script {
                    docker.withRegistry('https://registry.reitoria-edu.lab', 'dockerharbor') {
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
