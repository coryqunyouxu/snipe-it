pipeline {
    agent any
    stages {
         stage('check org') {
            steps {
                script {
                    sh 'microk8s kubectl describe pvc db -n snipeit'
                    sh 'microk8s kubectl describe pvc snipeit-claim0 -n snipeit'
                    sh 'microk8s kubectl describe pv db-pv -n snipeit'
                    sh 'microk8s kubectl describe pv snipeit-pv -n snipeit'
                }
            }
        }   
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    dir('deployment') {
                        sh 'microk8s kubectl apply -f "*.yaml" -n snipeit'
                        sh 'microk8s kubectl rollout restart deployment snipeit -n snipeit'
                        
                    }
                }
            }
        }
        stage('Check pvc') {
            steps {
                script {
                    sh 'microk8s kubectl get pvc -n snipeit'
                }
            }
        }
        stage('Enable Ingress') {
            steps {
                script {
                    sh 'microk8s enable ingress -n snipeit'
                }
            }
        }
        stage('Get Pods') {
            steps {
                script {
                    sh 'microk8s kubectl get pods -n snipeit'
                }
            }
        }
        stage('Get And Check Snipeit Service IP') {
            steps {
                script {
                    def snipeitIp = sh(script: "microk8s kubectl get svc snipeit -n snipeit '-o=jsonpath={.status.loadBalancer.ingress[0].ip}'", returnStdout: true).trim()
                    def snipeitPort = sh(script: "microk8s kubectl get svc snipeit -n snipeit '-o=jsonpath={.spec.ports[0].port}'", returnStdout: true).trim()
                    echo "Snipeit Service IP: ${snipeitIp}:${snipeitPort}"
                    sh "ping -c 5 ${snipeitIp}"
                }
            }
        }
    }
}
