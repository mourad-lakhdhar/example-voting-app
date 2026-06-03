pipeline {
    agent any

    environment {
        VERSION = "v${BUILD_NUMBER}"
        REPO = "mouradlakhdhar/voting-app-vote"
        GITOPS_REPO = "https://github.com/mourad-lakhdhar/example-voting-app.git"

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

                        docker push $REPO:$VERSION
                        docker push $REPO:latest
                    '''
                }
            }
        }

        stage('Update GitOps repository') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'github-token',
                        usernameVariable: 'GITHUB_USER',
                        passwordVariable: 'GITHUB_TOKEN'
                    )
                ]) {
                    sh """
                        rm -rf gitops

                        git clone git@github.com:mourad-lakhdhar/example-voting-app.git gitops

                        cd gitops

                        git config user.email "jenkins@local"
                        git config user.name "jenkins"

                        sed -i 's|mouradlakhdhar/voting-app-vote:.*|mouradlakhdhar/voting-app-vote:${VERSION}|' k8s-specifications/vote-deployment.yaml

                        git add k8s-specifications/vote-deployment.yaml

                        git commit -m "Update vote image to ${VERSION}" || true

                        git push origin main
                    """
                }
            }
        }    
    }
}