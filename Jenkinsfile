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

        stage('Get service URL') {
            steps {
                script {
                    // 步驟3：從 Kubernetes 中獲取服務的 IP 地址和端口
                    def serviceUrl = sh(
                        script: 'microk8s kubectl get svc snipeit -o=jsonpath="{.status.loadBalancer.ingress[0].ip}:{.spec.ports[0].port}"',
                        returnStdout: true
                    ).trim()
                    env.SERVICE_URL = "http://${serviceUrl}"
                }
            }
        }

        stage('Display service URL') {
            steps {
                script {
                    // 步驟4：顯示服務的 URL
                    echo "Service URL: ${env.SERVICE_URL}"
                }
            }
        }
    }
}
