pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "myusername/microservices-demo:${env.BUILD_NUMBER}"
        KUBE_CONFIG = credentials('kubeconfig-jenkins') // your kubeconfig in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/microservices-demo/microservices-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-jenkins']) {
                    sh "kubectl set image deployment/microservices-demo microservices-demo=${DOCKER_IMAGE} -n default || kubectl apply -f k8s/deployment.yaml"
                    sh "kubectl apply -f k8s/service.yaml"
                }
            }
        }
    }

    post {
        success {
            echo "Build, push, and deployment successful!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
