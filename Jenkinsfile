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
        
    }
}
