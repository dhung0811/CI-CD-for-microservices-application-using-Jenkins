pipeline {
    agent {
        label 'jenkins-agent'
    }
    environment {
        REPO = "dhung0811/robot-shop"
        TAG = "latest"
        COMPOSE_FILE_MAIN = "docker-compose.yaml"
        COMPOSE_FILE_LOAD = "docker-compose-load.yaml"
    }
    stages{
        stage('Checkout from Git'){
            steps{
                    git branch: 'main', url: 'https://github.com/dhung0811/CI-CD-for-microservices-application-using-Jenkins.git'
            }
        }
        stage("Docker Build & Push") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'Docker') {
                        sh "docker compose -f $COMPOSE_FILE_MAIN -f $COMPOSE_FILE_LOAD build"
                        sh "docker compose -f $COMPOSE_FILE_MAIN -f $COMPOSE_FILE_LOAD push || true"
                    }
                }
            }
        }

        stage('Deploy Services') {
            steps {
                sh "docker compose -f $COMPOSE_FILE_MAIN -f $COMPOSE_FILE_LOAD up -d"
            }
        }
    }
}
