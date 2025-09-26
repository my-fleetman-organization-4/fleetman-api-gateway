pipeline {
    agent { label 'jenkins-agent-docker' }

    environment {
        SERVICE_NAME = "fleetman-api-gateway"
        REPOSITORY_TAG = "${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }

    stages {
        stage('Preparation') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build and Push Image') {
            steps {
                sh 'docker build -t ${REPOSITORY_TAG} .'
                // sh 'docker push ${REPOSITORY_TAG}' // si quieres push a DockerHub
            }
        }

        stage('Deploy to Cluster') {
            steps {
                sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
            }
        }
    }

    post {
        always {
            echo "Pipeline finalizado, limpieza opcional si es necesario."
        }
    }
}

