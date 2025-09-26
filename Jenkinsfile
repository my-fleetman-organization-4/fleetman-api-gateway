pipeline {
  agent { label 'docker-agent' }  // label del PodTemplate en Kubernetes

  environment {
    SERVICE_NAME = "fleetman-api-gateway"
    REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
  }

  stages {
    stage('Preparation') {
      steps {
        container('builder') {  // ejecuta dentro del contenedor builder
          cleanWs()
          checkout scm
        }
      }
    }

    stage('Build') {
      steps {
        container('builder') {
          sh 'mvn clean package'
        }
      }
    }

    stage('Build and Push Image') {
      steps {
        container('builder') {
          sh 'docker image build -t ${REPOSITORY_TAG} .'
          sh 'docker push ${REPOSITORY_TAG}'
        }
      }
    }

    stage('Deploy to Cluster') {
      steps {
        container('builder') {
          sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
        }
      }
    }
  }
}

