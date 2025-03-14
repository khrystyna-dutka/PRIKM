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
                withDockerRegistry([credentialsId: 'dockerhub_token', url: '']) {
                    sh 'docker push khrystynadutka/prikm:latest'
                    sh 'docker push khrystynadutka/prikm:4'
                }
            }
        }
        
        stage('Deploy image') {
            steps {
                echo 'Deploying container...'

                // Stop any container using port 8081 (if it's running)
                sh '''
                    CONTAINER_ID=$(docker ps -q -f "publish=8081")
                    if [ -n "$CONTAINER_ID" ]; then
                        echo "Stopping container using port 8081..."
                        docker stop $CONTAINER_ID
                        docker rm $CONTAINER_ID
                    fi
                '''
                
                // Run the new container on port 8081
                sh 'docker run -d -p 8081:80 khrystynadutka/prikm:latest'
            }
        }
    }
}