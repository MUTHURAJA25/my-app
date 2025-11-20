pipeline {
    agent any

    stages {

        stage('SCM Checkout') {
            steps {
                git 'https://github.com/damodaranj/my-app.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh "mvn clean package"
                sh "mv target/myweb*.war target/newapp.war"
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
                    sh "docker login -u saidamo -p ${dockerPassword}"
                }
                sh 'docker push saidamo/myweb:0.0.2'
            }
        }

        stage('Kubernetes Deployment') {
            steps {
                sh """
                    echo 'Deploying to Kubernetes...'
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                    echo 'Checking pods...'
                    kubectl get pods

                    echo 'Checking service...'
                    kubectl get svc
                """
            }
        }
    }
}
