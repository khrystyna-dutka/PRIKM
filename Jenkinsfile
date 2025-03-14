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
                            <p>New version deployed!</p>
                        </body>
                    </html>" > index.html
                '''
            }
        }

        stage("Image build") {
            steps {
                sh "docker build -t prikm:latest ."
                sh "docker tag prikm khrystynadutka/prikm:latest"
                sh "docker tag prikm khrystynadutka/prikm:4"
            }
        }

        stage("Push to registry") {
            steps {
                withDockerRegistry([credentialsId: "dockerhub_token", url: ""]) {
                    sh "docker push khrystynadutka/prikm:latest"
                    sh "docker push khrystynadutka/prikm:4"
                }
            }
        }

        stage("Deploy image") {
            steps {
                echo "Deploying container..."
                sh '''
                    CONTAINER_ID=$(docker ps -q -f "publish=8081")
                    if [ -n "$CONTAINER_ID" ]; then
                        echo "Stopping existing container..."
                        docker stop $CONTAINER_ID
                        docker rm $CONTAINER_ID
                    fi
                '''
                sh "docker run -d -p 8081:80 khrystynadutka/prikm:latest"
            }
        }

        stage("Post-build Info") {
            steps {
                echo "Build and Deployment Successful!"
                sh "docker images | grep prikm"
            }
        }
    }
}
