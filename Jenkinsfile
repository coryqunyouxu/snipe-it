pipeline {
    agent any
    stages {
        stage('Build and push Docker image') {
            steps {
                script {
                    sh 'docker build . -t 172.23.8.1:9500/snipeit:latest --no-cache'
                    sh 'docker image push 172.23.8.1:9500/snipeit:latest'
                }
            }
        }
        stage('Delete PVC') {
            steps {
                script {
                    sh 'microk8s kubectl delete persistentvolumeclaim db'
                    sh 'microk8s kubectl delete persistentvolumeclaim snipeit-claim0'
                    sh 'microk8s kubectl delete persistentvolume db-pv'
                    sh 'microk8s kubectl delete persistentvolume snipeit-pv'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    dir('deployment') {
                        sh 'microk8s kubectl apply -f "*.yaml"'
                        sh 'microk8s kubectl rollout restart deployment snipeit'
                    }
                }
            }
        }
        
        stage('describe pvc and storageclass ') {
            steps {
                script {
                    sh 'microk8s kubectl describe pvc db'
                    sh 'microk8s kubectl describe pvc snipeit-claim0'
                    sh 'microk8s kubectl describe storageclass '
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
