pipeline {
    

        
    

    environment {
        // Variables de ejemplo, ajusta según tu cluster
        KUBECONFIG = '/home/jenkins/.kube/config'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Checkout') {
            steps {
                git url: 'https://github.com/my-fleetman-organization-4/fleetman-api-gateway', branch: 'master', credentialsId: 'GitHub'
            }
        }

        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                container('docker') {
                    sh '''
                    docker build -t myregistry/my-app:latest .
                    docker push myregistry/my-app:latest
                    '''
                }
            }
        }

        stage('Deploy to Cluster') {
            steps {
                container('kubectl') {
                    sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl rollout status deployment/my-app -n my-namespace
                    '''
                }
            }
        }
    }
}
