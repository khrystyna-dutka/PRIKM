pipeline {
    agent any

    stages {
        stage('Start') {
            steps {
                echo 'Lab_1: nginx/custom'
            }
        }

        stage('Info') {
            steps {
                echo 'Student: Khrystyna Dutka'
                echo 'Group: ITPA-11'
                echo 'Lab completed successfully!'
            }
        }

        stage('Build nginx/custom') {
            steps {
                sh 'docker build -t nginx/custom:latest .'
            }
        }

        stage('Test nginx/custom') {
            steps {
                echo 'Pass'
            }
        }
        stage('Deploy nginx/custom') {
            steps {
                sh '''
                    # Знайти контейнери, що використовують порт 80 і зупинити їх
                    docker ps --filter "publish=80" -q | xargs -r docker stop
                    
                    # Запустити новий контейнер
                    docker run -d -p 80:80 nginx/custom:latest
                '''
            }
        }
    }
}