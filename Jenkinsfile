pipeline {
    agent any

    environment {
        IMAGE_NAME = "harunsert/nginx-jenkins-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/HarunSert/mini-ci-cd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${IMAGE_NAME}:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/nginx-deploy nginx=${IMAGE_NAME}:latest --record
                '''
            }
        }
    }
}
