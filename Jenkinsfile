pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Start') {
            steps {
                echo 'Lab_2: started by GitHub'
            }
        }

        stage('Image build') {
            steps {
                sh 'docker build -t prikm:latest .'
                sh 'docker tag prikm khrystynadutka/prikm:latest'
                sh 'docker tag prikm khrystynadutka/prikm:4'
            }
        }

        stage('Push to registry') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub_token', url: 'https://index.docker.io/v1/']) {
                    sh 'docker push khrystynadutka/prikm:latest'
                    sh 'docker push khrystynadutka/prikm:4'
                }
            }
        }

        stage('Deploy image') {
            steps {
                echo 'Deploying container...'
                // Stop any container that is using port 8080
                sh '''
                    CONTAINER_ID=$(docker ps -q -f "ancestor=khrystynadutka/prikm:latest" -f "publish=8080")
                    if [ -n "$CONTAINER_ID" ]; then
                        echo "Stopping container using port 8080..."
                        docker stop $CONTAINER_ID
                    fi
                '''
                // Run the new container
                sh 'docker run -d -p 8080:80 khrystynadutka/prikm:latest'
            }
        }
    }
}
