pipeline {
  agent {
             kubernetes {

        
                 
             yaml """
apiVersion: v1
kind: Pod
spec:
spec:
  serviceAccountName: jenkins-sa
  containers:
  - name: maven
    image:  israel452/maven-docker:2.0
    command:
    - cat
    tty: true
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    command: ["cat"]
    tty: true
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock

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
       }
 
       stage('Build and Push Image') {
            steps {
                  container('kaniko') {
                    sh """
                      /kaniko/executor \
                      --context ${WORKSPACE} \
                      --dockerfile ${WORKSPACE}/Dockerfile \
                      --destination ${REPOSITORY_TAG}
                    """
                }
            }
         }
       }
 
       stage('Deploy to Cluster') {
           steps {
                container('maven') {
                    sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
                }
           }
       }
    }
 }

