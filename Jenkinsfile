pipeline {
    agent any 
    stages {
        stage ('Verify cluster access') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8s-creds', namespace: 'webapps', serverUrl: 'https://4A8CB4ED6E0779D33EDCB150EF63DF27.gr7.ap-south-1.eks.amazonaws.com']]) {
                      sh "kubectl get pods"
                    }
                }
            }
        }
        stage ('deploy micro services into k8s') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8s-creds', namespace: 'webapps', serverUrl: 'https://4A8CB4ED6E0779D33EDCB150EF63DF27.gr7.ap-south-1.eks.amazonaws.com']]) {
                       sh "kubectl apply -f deployment-service.yml"
                       sh "spleep 60"
                       sh "kubectl get pods"
                    }
                }
            }
        } 
     }
}
