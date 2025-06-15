pipeline {
    agent {
        label 'jenkins-agent'
    }
    environment {
        REPO = "dhung0811"
        TAG = "latest"
        COMPOSE_FILE_MAIN = "docker-compose.yaml"
        COMPOSE_FILE_LOAD = "docker-compose-load.yaml"
        SONAR_TOKEN = credentials('sonar') // Jenkins Credentials ID
    }
    tools {
        sonarScanner 'SonarScanner 7.x' // Your scanner name
    }
    stages{
        stage('Checkout from Git'){
            steps{
                    git branch: 'main', url: 'https://github.com/dhung0811/CI-CD-for-microservices-application-using-Jenkins.git'
            }
        }
        stage('SonarCloud Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner 7.x'
                    withSonarQubeEnv('SonarCloud') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
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
                sh "docker compose -f $COMPOSE_FILE_MAIN -f $COMPOSE_FILE_LOAD up"
            }
        }
    }
}
