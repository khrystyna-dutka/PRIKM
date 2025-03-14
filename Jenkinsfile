pipeline {
    agent any

    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Start") {
            steps {
                echo "Lab_2: started by GitHub"
            }
        }

        stage("Modify Web Page") {
            steps {
                sh '''
                    echo "<!doctype html>
                    <html lang=\\"en\\">
                        <head>
                            <meta charset=\\"utf-8\\">
                            <title>Lab_2</title>
                        </head>
                        <body>
                            <h2>Hello from Docker, launched by Jenkins, triggered by GitHub</h2>
                            <p>Build version: $(date)</p>
                        </body>
                    </html>" > index.html
                '''
            }
        }

        stage("Image build") {
            steps {
                script {
                    def BUILD_VERSION = new Date().format("yyyyMMddHHmmss")
                    sh "docker build -t prikm:${BUILD_VERSION} ."
                    sh "docker tag prikm:${BUILD_VERSION} khrystynadutka/prikm:latest"
                    sh "docker tag prikm:${BUILD_VERSION} khrystynadutka/prikm:${BUILD_VERSION}"
                }
            }
        }

        stage("Push to registry") {
            steps {
                withDockerRegistry([credentialsId: "dockerhub_token", url: ""]) {
                    script {
                        def BUILD_VERSION = new Date().format("yyyyMMddHHmmss")
                        sh "docker push khrystynadutka/prikm:latest"
                        sh "docker push khrystynadutka/prikm:${BUILD_VERSION}"
                    }
                }
            }
        }

        stage("Deploy image") {
            steps {
                echo "Deploying container..."
                script {
                    sh '''
                        CONTAINER_ID=$(docker ps -q -f "publish=8081")
                        if [ -n "$CONTAINER_ID" ]; then
                            echo "Stopping existing container..."
                            docker stop $CONTAINER_ID
                            docker rm $CONTAINER_ID
                        fi
                    '''
                    def BUILD_VERSION = new Date().format("yyyyMMddHHmmss")
                    sh "docker run -d -p 8081:80 khrystynadutka/prikm:latest"
                }
            }
        }

        stage("Check DockerHub Artifacts") {
            steps {
                echo "Checking DockerHub repository..."
                sh "curl -s https://hub.docker.com/v2/repositories/khrystynadutka/prikm/tags/ | jq '.count'"
            }
        }
    }
}
