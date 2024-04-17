pipeline {
    agent any
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

    }
}
