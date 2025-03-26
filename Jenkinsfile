pipeline {
    agent any

    stages {
        stage('Start') {
            steps {
                echo 'Lab_2: started by GitHub'
            }
        }

        stage('Insert Build Version') {
            steps {
                echo "Inserting build version into index.html..."
                sh """
                    sed -i '/<!-- Jenkins will insert build info here -->/a <p><strong>Build number:</strong> ${BUILD_NUMBER}</p>' index.html
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t prikm:latest ."
                sh "docker tag prikm khrystynadutka/prikm:latest"
                sh "docker tag prikm khrystynadutka/prikm:${BUILD_NUMBER}"
                sh "docker tag prikm khrystynadutka/prikm:dev"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub_token', url: '']) {
                    sh "docker push khrystynadutka/prikm:latest"
                    sh "docker push khrystynadutka/prikm:${BUILD_NUMBER}"
                    sh "docker push khrystynadutka/prikm:dev"
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                echo "Checking and stopping old container (if running)..."
                sh '''
                    CONTAINER_ID=$(docker ps -q --filter "publish=80")
                    if [ -n "$CONTAINER_ID" ]; then
                        echo "Stopping container $CONTAINER_ID"
                        docker stop $CONTAINER_ID
                        docker rm $CONTAINER_ID
                    fi
                '''
                echo "Running new container..."
                sh "docker run -d -p 80:80 khrystynadutka/prikm:${BUILD_NUMBER}"
            }
        }

        stage('Check DockerHub Artifacts') {
            steps {
                script {
                    def output = sh(
                        script: "curl -s https://hub.docker.com/v2/repositories/khrystynadutka/prikm/tags/ | grep -o '\"name\":' | wc -l",
                        returnStdout: true
                    ).trim()

                    def count = output.toInteger()
                    echo "Current artifact count: ${count}"

                    if (count < 2) {
                        error("Недостатньо артефактів на DockerHub (менше 2). Лабораторну не зараховано.")
                    } else {
                        echo "Умова виконана: є щонайменше 2 артефакти на DockerHub."
                    }
                }
            }
        }
    }
}
