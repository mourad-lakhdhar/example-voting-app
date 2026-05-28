pipeline {
    agent any

    stages {

        stage('Build vote image') {
            steps {
                dir('vote') {
                    sh 'docker build -t voting-app-vote:v1 .'
                }
            }
        }
    }
}