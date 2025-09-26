pipeline {
    agent {
        docker {
            label 'jenkins-agent-docker'
    }

    environment {
        SERVICE_NAME = "fleetman-api-gateway"
        // Asegúrate de definir estas variables en Jenkins
        // Global Environment Variables o Pipeline Parameters
        REPOSITORY_TAG = "${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }

    stages {
        stage('Preparation') {
            steps {
                // Limpiar workspace antes de iniciar
                cleanWs()
                // Clonar código desde SCM
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Compilar con Maven
                sh 'mvn clean package'
            }
        }

        stage('Build and Push Image') {
            steps {
                // Construir imagen Docker usando socket del host
                sh 'docker build -t ${REPOSITORY_TAG} .'
                // Optional: hacer push si quieres subir a DockerHub
                // sh 'docker push ${REPOSITORY_TAG}'
            }
        }

        stage('Deploy to Cluster') {
            steps {
                // Desplegar en tu cluster local
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
}
