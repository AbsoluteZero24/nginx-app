pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/AbsoluteZero24/nginx-app.git'
            }
        }

        stage('compile code') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('code test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('sonar analsys') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=nginx-app \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=nginx-app '''
                }
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