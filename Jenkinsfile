pipeline {
    agent any

    stages {
        stage('Start') {
            steps {
                echo 'Lab_2: started by GitHub'
            }
        }
        stage('Image build') {
            steps {
                sh "docker build -t prikm:latest ."
                sh "docker tag prikm khrystyna/prikm:latest"
                sh "docker tag prikm hrystyna/prikm:$BUILD_NUMBER"
            }
        }
        stage('Push to registry') {
            steps {
                withDockerRegistry([ credentialsId: "dockerhub_token", url: "https://index.docker.io/v1/" ])
                {
                    sh "docker push hrystyna_dockerhub/prikm:latest"
                    sh "docker push hrystyna_dockerhub/prikm:$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy image') {
            steps{
                sh "docker run -d -p 80:80 hrystyna_dockerhub/prikm"
            }
        }
    }
}