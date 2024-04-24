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
        stage('Check Ingress') {
            steps {
                sh 'microk8s kubectl get ingress'
            }
        }
        stage('Get Service URL') {
            agent any
            steps {
                script {
                    // 使用 minikube ip 命令返回的 IP 地址
                    def minikubeIp = sh(script: 'minikube ip', returnStdout: true).trim()
                    
                    // 構建 Ingress URL
                    env.SNIPEIT_INGRESS_URL = "http://${minikubeIp}"
                    
                    // 將域名對應到 minikube IP，並將結果添加到 /etc/hosts 文件中
                    sh "echo '${minikubeIp} snipeit.services.com' >> /etc/hosts"
                }
                echo "Ingress URL: ${env.SNIPEIT_INGRESS_URL}"
            }
        }
    }
}
