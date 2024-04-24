pipeline {
    agent any
    environment {
        MINIKUBE_IP = sh(script: 'minikube ip', returnStdout: true).trim()
        DNS_NAME = sh(script: 'minikube ip').trim().replace('.', '-') + '.nip.io'
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
                    sh "sed -i 's/snipeit.services.com/${DNS_NAME}/g' ingress.yaml"
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
    }
    post {
        always {
            script {
                def snipeitUrl = "http://${MINIKUBE_IP}:8000"
                echo "You can access SnipeIT at: ${snipeitUrl}"
            }
        }
    }
}

