pipeline {
    agent any
    environment {
        K8S_PORT = 53474
        TARGET = 'aws'
    }
    stages {
        stage('Build Auth') {
            steps {
                build job: 'organice-auth', wait: true
            }
        }
        stage('Jenkins Gateway') {
            steps {
                echo 'Jenkins Gateway'
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn clean package'
            }
        }      
        stage('Build Image') {
            steps {
                script {
                    gateway = docker.build("alfredjynx/gateway:${env.BUILD_ID}", "-f Dockerfile .")
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        gateway.push("${env.BUILD_ID}")
                        gateway.push("latest")
                    }
                }
            }
        }

        stage('Deploy on Local K8s') {
            when { 
                environment name: 'TARGET', value: 'local' 
            }
            steps {
                withCredentials([ string(credentialsId: 'minikube-credential', variable: 'api_token') ]) {
                    sh "kubectl --token $api_token --server https://host.docker.internal:${env.K8S_PORT}  --insecure-skip-tls-verify=true apply -f ./k8s/deployment.yaml"
                    sh "kubectl --token $api_token --server https://host.docker.internal:${env.K8S_PORT}  --insecure-skip-tls-verify=true apply -f ./k8s/service.yaml"
                }
            }
        }
        stage('Deploy on AWS K8s') {
            when { 
                environment name: 'TARGET', value: 'aws' 
            }
            steps {
                withCredentials([ string(credentialsId: 'minikube-credential', variable: 'api_token') ]) {
                    sh "kubectl apply -f ./k8s/deployment.yaml"
                    sh "kubectl apply -f ./k8s/service.yaml"
                }
            }
        }
    }
}