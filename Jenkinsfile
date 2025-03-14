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
                echo "Lab_2: started by GitHub, build #${env.BUILD_NUMBER}"
            }
        }

        stage('Update Webpage') {
            steps {
                sh '''
                    sed -i "s/<title>Lab_2<\/title>/<title>Lab_2 - Build ${BUILD_NUMBER}<\/title>/" index.html
                '''
            }
        }

        stage('Image build') {
            steps {
                sh 'docker build -t prikm:latest .'
                sh 'docker tag prikm khrystynadutka/prikm:latest'
                sh 'docker tag prikm khrystynadutka/prikm:build-${BUILD_NUMBER}'
            }
        }

        stage('Push to registry') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub_token', url: '']) {
                    sh 'docker push khrystynadutka/prikm:latest'
                    sh 'docker push khrystynadutka/prikm:build-${BUILD_NUMBER}'
                }
            }
        }

        stage('Test Image') {
            steps {
                sh '''
                    docker run --rm -d -p 8082:80 --name test_container khrystynadutka/prikm:latest
                    sleep 5
                    if curl -s http://localhost:8082 | grep -q "Hello from Docker"; then
                        echo "Test passed!"
                    else
                        echo "Test failed!" && exit 1
                    fi
                    docker stop test_container
                '''
            }
        }

        stage('Check Artifacts') {
            steps {
                script {
                    def artifactCount = sh(script: "curl -s https://hub.docker.com/v2/repositories/khrystynadutka/prikm/tags/ | jq '.count'", returnStdout: true).trim()
                    echo "DockerHub artifacts: ${artifactCount}"
                    if (artifactCount.toInteger() <= 2) {
                        error("Not enough artifacts in DockerHub. Keep building!")
                    }
                }
            }
        }

        stage('Deploy image') {
            steps {
                echo 'Deploying container...'
                sh '''
                    CONTAINER_ID=$(docker ps -q -f "publish=8081")
                    if [ -n "$CONTAINER_ID" ]; then
                        echo "Stopping container using port 8081..."
                        docker stop $CONTAINER_ID
                        docker rm $CONTAINER_ID
                    fi
                '''
                sh 'docker run -d -p 8081:80 khrystynadutka/prikm:latest'
            }
        }
    }
}