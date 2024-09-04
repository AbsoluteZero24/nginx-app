pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/AbsoluteZero24/nginx-app.git'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'docker') {
                        sh "docker build -t absolutezero24/nginx-app:latest ."
                    }
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'docker') {
                        sh "docker push absolutezero24/nginx-app:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'kubernetes-cred', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.100.100:6443') {
                    sh "kubectl apply -f k8s-deployment.yml"
                    }
                }
            }
        }
    }
}