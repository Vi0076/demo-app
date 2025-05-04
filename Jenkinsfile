pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vi007/appserver"
        DOCKER_CREDENTIALS_ID = 'dockerhub-creds'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Vi0076/demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    sh """
                        docker rm -f demo-app || true
                        docker pull ${DOCKER_IMAGE}:latest
                        docker run -d --name demo-app -p 3000:3000 ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            emailext(
                to: 'ananda.yashaswi@quokkalabs.com, prateek.roy@quokkalabs.com',
                subject: "✅ Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The latest build and deployment succeeded.\n\nCheck the app at http://<server-ip>:3000"
            )
        }
        failure {
            emailext(
                to: 'ananda.yashaswi@quokkalabs.com, prateek.roy@quokkalabs.com',
                subject: "❌ Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build failed. Please check the Jenkins console logs for details."
            )
        }
    }
}
