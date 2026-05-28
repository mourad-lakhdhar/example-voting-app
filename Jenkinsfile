pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/mourad-lakhdhar/example-voting-app.git'
            }
        }

        stage('Build vote image') {
            steps {
                dir('vote') {
                    sh 'docker build -t voting-app-vote:v1 .'
                }
            }
        }
    }
}