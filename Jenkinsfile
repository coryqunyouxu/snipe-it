pipeline {
    agent any
    stages {
         stage('check org') {
            steps {
                script {
                    sh 'microk8s kubectl describe pvc db'
                    sh 'microk8s kubectl describe pvc snipeit-claim0'
                    sh 'microk8s kubectl describe pv db-pv'
                    sh 'microk8s kubectl describe pv snipeit-pv'
                }
            }
        }   
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    dir('deployment') {
                        sh 'microk8s kubectl apply -f "*.yaml -n snipeit'
                        sh 'microk8s kubectl rollout restart deployment snipeit'
                        sh 'microk8s kubectl rollout restart deployment redis'
                        sh 'microk8s kubectl rollout restart deployment mariadb'
                        sh 'microk8s kubectl rollout restart deployment mailhog'
                    }
                }
            }
        }
        stage('Check pvc') {
            steps {
                script {
                    sh 'microk8s kubectl get pvc'
                }
            }
        }
        stage('Enable Ingress') {
            steps {
                script {
                    sh 'microk8s enable ingress'
                }
            }
        }
        stage('Get Pods') {
            steps {
                script {
                    sh 'microk8s kubectl get pods'
                    sh 'microk8s kubectl logs mariadb-5878b7646c-j5jpb'
                }
            }
        }
        stage('Describe Snipeit Pod') {
            steps {
                script {
                    sh 'microk8s kubectl describe node'
                }
            }
        }
        stage('Get And Check Snipeit Service IP') {
            steps {
                script {
                    def snipeitIp = sh(script: "microk8s kubectl get svc snipeit '-o=jsonpath={.status.loadBalancer.ingress[0].ip}'", returnStdout: true).trim()
                    def snipeitPort = sh(script: "microk8s kubectl get svc snipeit '-o=jsonpath={.spec.ports[0].port}'", returnStdout: true).trim()
                    echo "Snipeit Service IP: ${snipeitIp}:${snipeitPort}"
                    sh "ping -c 5 ${snipeitIp}"
                }
            }
        }
    }
}
