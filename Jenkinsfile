pipeline {
    agent any

    stages {
        stage('Build and push Docker image') {
            steps {
                script {
                    // 步驟1：構建 Docker 映像並推送到容器註冊表
                    sh 'docker build . -t 172.23.8.1:9500/snipeit:latest --no-cache'
                    sh 'docker image push 172.23.8.1:9500/snipeit:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // 步驟2：部署到 Kubernetes 集群
                    dir('deployment') {
                        sh 'microk8s kubectl apply -f "*.yaml"'
                        sh 'microk8s kubectl rollout restart deployment snipeit'
                    }
                }
            }
        }

        stage('Change service type to LoadBalancer') {
            steps {
                script {
                    // Change service type to LoadBalancer
                    sh 'kubectl patch svc/snipeit -p \'{\"spec\": {\"type\": \"LoadBalancer\"}}\''
                    
                    // Get updated service details
                    sh 'kubectl get service snipeit'
                }
            }
        }
    }
}
