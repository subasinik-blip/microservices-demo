pipeline {
    agent any

    environment {
        IMAGE = "subasinik/frontend-service:v2"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/subasinik-blip/microservices-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE ./rendor-demo'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/frontend frontend=$IMAGE
                '''
            }
        }
    }
}
