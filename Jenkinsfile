pipeline {
    agent any

    tools {
        maven 'maven3'     // Jenkins will install Maven automatically
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/damodaranj/my-app.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
                sh 'mv target/myweb*.war target/newapp.war'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t saidamo/myweb:0.0.2 .'
            }
        }

        stage('DockerHub Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                    sh "echo ${dockerPassword} | docker login -u saidamo --password-stdin"
                }
                sh 'docker push saidamo/myweb:0.0.2'
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                sh """
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                """
            }
        }

        stage('Verify') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }
}
