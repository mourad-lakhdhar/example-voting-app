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

        stage('Setup SSH known_hosts') {
            steps {
                sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
                    ssh-keyscan -t ed25519 github.com >> ~/.ssh/known_hosts
                '''
            }
        }        

        stage('Update GitOps repository') {
                when {
                   not {
                        changelog '.*\\[skip ci\\].*'
                    }
                }

            steps {
                sshagent(['github-ssh']) {
                    sh """
                        rm -rf gitops

                        git clone git@github.com:mourad-lakhdhar/example-voting-app.git gitops

                        cd gitops

                        git config user.email "jenkins@local"
                        git config user.name "jenkins"

                        sed -i 's|mouradlakhdhar/voting-app-vote:.*|mouradlakhdhar/voting-app-vote:${VERSION}|' k8s-specifications/vote-deployment.yaml

                        git add k8s-specifications/vote-deployment.yaml
                        git commit -m "[skip ci] Update vote image to ${VERSION}" || true

                        git push origin main
                    """
                }
            }
        }
    }
}