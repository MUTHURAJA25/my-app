pipeline {
    agent any

    stages {

        stage('SCM Checkout') {
            steps {
                git 'https://github.com/damodaranj/my-app.git'
            }
        }

        stage('Compile & Package') {
            steps {
                sh "mvn clean package"
                sh "mv target/myweb*.war target/newapp.war"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t saidamo/myweb:0.0.2 .'
            }
        }

        stage('Docker Image Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                    sh "docker login -u saidamo -p ${dockerPassword}"
                }
                sh 'docker push saidamo/myweb:0.0.2'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                """
            }
        }

    }
}
