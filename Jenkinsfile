pipeline {
    agent any
    stages {
        stage('check org') {
            steps {
                script {
                    sh 'microk8s kubectl describe pvc db-claim -n snipeit'
                    sh 'microk8s kubectl describe pvc snipeit-claim -n snipeit'
                    sh 'microk8s kubectl describe pv db-pv-test -n snipeit'
                    sh 'microk8s kubectl describe pv snipeit-pv-test -n snipeit'
                }
            }
        } 
        stage('Check svc') {
            steps {
                script {
                    sh 'microk8s kubectl get svc -n snipeit'
                }
            }
        }
        
        stage('Check ingress') {
            steps {
                script {
                    sh 'microk8s kubectl get ingress -n snipeit'
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
