pipeline {
    agent any

    environment {
        VERSION = "v${BUILD_NUMBER}"
        REPO = "mouradlakhdhar/voting-app-vote"
    }

    stages {

        stage('Build vote image') {
            steps {
                dir('vote') {
                    sh """
                        docker build \
                          -t ${REPO}:${VERSION} \
                          -t ${REPO}:latest .
                    """
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                        docker push '"${REPO}"':'"${VERSION}"'
                        docker push '"${REPO}"':latest
                    '''
                }
            }
        }
    }
}