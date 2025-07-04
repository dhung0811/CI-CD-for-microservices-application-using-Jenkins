pipeline {
    agent {
        label 'jenkins-agent'
    }
    environment {
        REPO = "dhung0811"
        TAG = "latest"
        COMPOSE_FILE_MAIN = "docker-compose.yaml"
        COMPOSE_FILE_LOAD = "docker-compose-load.yaml"
        SCANNER_HOME = tool 'sonar'
        NVD_API_KEY = credentials('NVD_API_KEY')
    }
    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/dhung0811/CI-CD-for-microservices-application-using-Jenkins.git'
            }
        }

        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=robot-shop \
                        -Dsonar.projectKey=robot-shop \
                        -Dsonar.exclusions=**/*.java'''
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

        stage('Trivy Security Scan') {
            steps {
                script {
                    def images = [
                        'rs-cart', 'rs-catalogue', 'rs-dispatch', 'rs-load', 'rs-mongodb', 'rs-mysql-db',
                        'rs-payment', 'rs-ratings', 'rs-shipping', 'rs-user', 'rs-web'
                    ]
                    images.each { img ->
                        def fullImage = "${REPO}/${img}:${TAG}"
                        sh """
                            echo " Scanning ${fullImage}..."
                            trivy image --severity HIGH,CRITICAL --no-progress --exit-code 0 ${fullImage} || echo " Scan issues in ${fullImage}"
                        """
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
