pipeline {
   //agent any
   agent{
     kubernetes {
               yaml '''
   apiVersion: v1
   kind: Pod
   spec:
     containers:
     - name: jnlp
       image: jenkins/inbound-agent:3341.v0766d82b_dec0-1
       args: ['\$(JENKINS_SECRET)', '\$(JENKINS_NAME)']
       volumeMounts:
       - mountPath: /home/jenkins/agent
         name: workspace-volume
     - name: maven
       image: maven:3.8.6-openjdk-11
       command: ['cat']
       tty: true
       volumeMounts:
       - mountPath: /home/jenkins/agent
         name: workspace-volume
     volumes:
     - name: workspace-volume
       emptyDir: {}
   '''
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
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh '''mvn clean package'''
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

