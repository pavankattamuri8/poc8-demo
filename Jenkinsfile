pipeline {
    agent any
 
    environment {
        SCANNER_HOME    = tool 'sonar-scanner'
        SONAR_SERVER    = 'demo'
        DOCKER_HUB_USER = 'venkatapavank'
        IMAGE_NAME      = 'my-app'
        IMAGE_TAG       = "${BUILD_NUMBER}"
    }
 
    stages {
 
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/pavankattamuri8/poc8-demo.git'
            }
        }
 
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONAR_SERVER}") {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=my-poc"
                }
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
 
        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }
    }
}
