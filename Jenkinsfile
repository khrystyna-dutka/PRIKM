pipeline {
    agent any

    environment {
        IMAGE_NAME = "khrystynadutka/prikm"
        IMAGE_TAG = "lab7"
    }

    stages {
        stage('Get Secrets and Docker Login') {
            steps {
                script {
                    def dockerUser = sh(script: "hcp vault-secrets secrets open docker_username | grep 'Value:' | cut -d':' -f2-", returnStdout: true).trim()
                    def dockerPass = sh(script: "hcp vault-secrets secrets open docker_password | grep 'Value:' | cut -d':' -f2-", returnStdout: true).trim()

                    withEnv(["DOCKER_USERNAME=${dockerUser}", "DOCKER_PASSWORD=${dockerPass}"]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Test Run') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
    }
}
