pipeline {
    agent any
    environment {
        SNIPEIT_INGRESS_HOST = '192.168.49.2'
        SNIPEIT_INGRESS_PORT = '8000'
    }
    stages {
        stage('Build the docker image and push to registry.') {
            steps {
                sh 'docker build . -t 172.23.8.1:9500/snipeit:latest --no-cache'
                sh 'docker image push 172.23.8.1:9500/snipeit:latest'
            }
        }
        stage('Deploy to the minikube') {
            steps {
                dir('deployment') {
                    sh 'microk8s kubectl apply -f "*.yaml"'
                    sh 'microk8s kubectl rollout restart deployment snipeit'               
                }
            }
        }
        stage('Check Ingress') {
            steps {
                sh 'microk8s kubectl get ingress'
            }
        }
        stage('Get Service URL') {
            agent any
            steps {
                script {
                    // 使用環境變量構建 Ingress URL
                    env.SNIPEIT_INGRESS_URL = "http://${SNIPEIT_INGRESS_HOST}:${SNIPEIT_INGRESS_PORT}"
                }
                echo "Ingress URL: ${env.SNIPEIT_INGRESS_URL}"
            }
        }
    }
}

