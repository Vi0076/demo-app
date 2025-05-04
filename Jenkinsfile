pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vi007/appserver"
        DOCKER_CREDENTIALS_ID= "dockerhub-creds"
    }

    stages {
        stage( 'checkout') {
            steps {
                git "https://github.com/Vi0076/demo-app.git"
            }
        }

        stage("build docker image") {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage("push to docker") {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1", "${DOCKER_CREDENTIALS_ID}")
                    dockerImage.push()
                }
            }
        }

        stage("run containe") {
            steps {
                docker.withRegistry("https://index.docker.io/v1", "${DOCKER_CREDENTIALS_ID}")

                sh """
                docker rm -f nodejs-app || true
                docker run -d --name nodejs-app -p 3000:3000 ${DOCKER_IMAGE}:${BUILD_NUMBER}
                """
            }
        }
    }
}