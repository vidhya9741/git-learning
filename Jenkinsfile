pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "adminchavi/jenkins-docker-demo"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Tag Latest') {
            steps {
                sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }
    }

    post {
        success {
            echo 'Docker image successfully built and pushed to Docker Hub!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
