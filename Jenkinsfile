pipeline {
    agent {
        label 'jenkins-agent'
    }

    environment {
        REPO = "dhung0811"
        TAG = "latest"
        COMPOSE_FILE_MAIN = "docker-compose.yaml"
        COMPOSE_FILE_LOAD = "docker-compose-load.yaml"
        SCANNER_HOME = tool 'sonar'  // This is the name you gave in Jenkins for SonarScanner
        NVD_API_KEY = credentials('NVD_API_KEY')
    }

    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/dhung0811/CI-CD-for-microservices-application-using-Jenkins.git'
            }
        }

        stage('Compile Java') {
            steps {
                sh 'mkdir -p out && find src -name "*.java" | xargs javac -d out'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') { // 'sonar-server' must match the SonarQube server name in Jenkins config
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=robot-shop \
                        -Dsonar.projectName=robot-shop \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=out \
                        -Dsonar.host.url=https://sonarcloud.io'''
                }
            }
        }

        stage('Docker Build & Push') {
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
