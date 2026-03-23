pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "subasinik/frontend-service"
        TAG = "v2"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/subasinik-blip/microservices-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE:$TAG ./rendor-demo
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh '''
                echo "YOUR_DOCKER_TOKEN" | docker login -u subasinik --password-stdin
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push $DOCKER_IMAGE:$TAG
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                docker run --rm \
                -v /var/jenkins_home/.kube:/root/.kube \
                -v $(pwd):/workspace \
                bitnami/kubectl:latest \
                kubectl apply -f /workspace/kubernetes-manifests/
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful"
        }
        failure {
            echo "❌ Pipeline Failed"
        }
    }
}
