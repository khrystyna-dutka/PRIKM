properties([
  parameters([
    string(name: 'DEPLOY_ENV', defaultValue: 'dev', description: 'Оберіть середовище: dev/staging/prod'),
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Запускати тести?')
  ])
])

pipeline {
    agent any

    stages {

        stage('Send Plan to Teams') {
            steps {
                script {
                    def plan = """
📋 **План виконання Jenkins Pipeline**
> Середовище: `${params.DEPLOY_ENV}`
> Запуск тестів: `${params.RUN_TESTS ? "Так" : "Ні"}`

| Стадія       | Що відбувається                                                                 |
|--------------|----------------------------------------------------------------------------------|
| Read Config  | Читає \`project_name\` з JSON-файлу                                              |
| Build        | Симулює збірку проєкту, створює build-файли                                     |
| Test         | Якщо обрано \`RUN_TESTS = true\`, виконує тести                                  |
| Deploy       | Виводить, у яке середовище відбувається деплой                                  |
| Post-section | Надсилає повідомлення в Teams залежно від результату виконання                  |
"""
                    office365ConnectorSend webhookUrl: 'ТВОЄ_ПОСИЛАННЯ_НА_WEBHOOK', message: plan
                }
            }
        }

        stage('Read Config (JSON)') {
            steps {
                script {
                    def jsonText = readFile 'config.json'
                    def config = readJSON text: jsonText
                    echo "Проєкт: ${config.project_name}"
                }
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Збірка проєкту...'
                sh 'mkdir -p build && echo "Build successful!" > build/result.txt'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo '🧪 Тести...'
                sh 'echo "Running tests..." && sleep 2 && echo "Tests passed!"'
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Деплой у середовище: ${params.DEPLOY_ENV}"
                sh "echo 'Deploying to ${params.DEPLOY_ENV} environment...' && sleep 1"
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline завершився успішно!'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "✅ Успішне виконання для середовища `${params.DEPLOY_ENV}`."
        }

        failure {
            echo '❌ Pipeline завершився з помилкою.'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "❌ Помилка під час виконання для середовища `${params.DEPLOY_ENV}`."
        }

        unstable {
            echo '⚠️ Pipeline завершився у нестабільному стані.'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "⚠️ Нестабільне виконання. Перевірте логи!"
        }
    }
}
