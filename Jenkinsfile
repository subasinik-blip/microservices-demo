pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t subasinik/frontend-service:v2 ./rendor-demo'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push subasinik/frontend-service:v2'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f kubernetes-manifests/'
            }
        }
    }
}
