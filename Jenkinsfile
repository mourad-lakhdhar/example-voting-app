pipeline {
    agent any

    environment {
        IMAGE_NAME = "mouradlakhdhar/voting-app-vote:v1"
    }

    stages {

        stage('Build vote image') {
            steps {
                dir('vote') {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'mouradlakhdhar',
                    passwordVariable: 'osboxes.org'
                )]) {

                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'

                    sh 'docker push $IMAGE_NAME'
                }
            }
        }
    }
}