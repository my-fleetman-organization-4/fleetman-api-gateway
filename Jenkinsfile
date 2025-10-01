pipeline {
  agent {
                 kubernetes {
             yaml """
 apiVersion: v1
 kind: Pod
 spec:
   containers:
   - name: maven
     image: maven:3.9.4-openjdk-21
     command:
     - cat
     tty: true
 """
         }
     }
 
    environment {
      // You must set the following environment variables
      // ORGANIZATION_NAME
      // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
 
      SERVICE_NAME = "fleetman-api-gateway"
      REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }
 
    stages {
       stage('Preparation') {
          steps {
             deleteDir() 
             git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
          }
       }
       stage('Build') {
          steps {
                 container('maven') {
                     sh 'mvn -v'
                     sh 'mvn clean package'
          }
       }
 
       stage('Build and Push Image') {
          steps {
            sh 'docker image build -t ${REPOSITORY_TAG} .'
          }
       }
 
       stage('Deploy to Cluster') {
           steps {
                     sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
           }
       }
    }
 }
}
