pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "vi007/appserver"
        DOCKER_HUB_USERNAME = credentials('username')
        DOCKER_HUB_PASSWORD = credentials('passwd')
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:Vi0076/demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh """
                            echo $passwd | docker login -u $username --password-stdin
                            docker image push ${DOCKER_IMAGE}:latest
                        """
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                docker rm -f demo-app || true
                docker pull ${DOCKER_IMAGE}:latest
                docker run -d --name demo-app -p 3000:3000 ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            emailext to: 'ananda.yashaswi@quokkalabs.com, prateek.roy@quokkalabs.com',
                    subject: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "The latest build and deployment succeeded."
        }
        failure {
            emailext to: 'ananda.yashaswi@quokkalabs.com, prateek.roy@quokkalabs.com',
                    subject: "Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "The build failed. Please check the Jenkins console for details."
        }
    }
}
