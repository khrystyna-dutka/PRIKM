properties([
  parameters([
    string(name: 'DEPLOY_ENV', defaultValue: 'dev', description: 'Choose environment: dev/staging/prod'),
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
  ])
])

pipeline {
    agent any

    stages {

        stage('Notify Plan to Teams') {
            steps {
                script {
                    def plan = """
📋 **Jenkins Pipeline Execution Plan**
> Environment: ${params.DEPLOY_ENV}
> Run tests: ${params.RUN_TESTS ? "Yes" : "No"}

| Stage         | Description                                                   |
|---------------|---------------------------------------------------------------|
| Read Config   | Reads 'project_name' from JSON config                         |
| Build         | Simulates project build, creates output files                 |
| Test          | Runs tests if RUN_TESTS = true                                |
| Deploy        | Simulates deployment to selected environment                  |
| Post Actions  | Sends result notifications to Teams                           |
"""
                    office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: plan
                }
            }
        }

        stage('Read Config (JSON)') {
            steps {
                script {
                    def jsonText = readFile 'config.json'
                    def config = readJSON text: jsonText
                    echo "Project name from config: ${config.project_name}"
                }
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building the project...'
                sh 'mkdir -p build && echo "Build completed!" > build/output.txt'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo '🧪 Running tests...'
                sh 'echo "Tests running..." && sleep 2 && echo "All tests passed!"'
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying to environment: ${params.DEPLOY_ENV}"
                sh "echo 'Deploying to ${params.DEPLOY_ENV}...' && sleep 1"
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "✅ Pipeline succeeded for environment: ${params.DEPLOY_ENV}"
        }

        failure {
            echo '❌ Pipeline failed.'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "❌ Pipeline failed for environment: ${params.DEPLOY_ENV}"
        }

        unstable {
            echo '⚠️ Pipeline is unstable.'
            office365ConnectorSend webhookUrl: 'https://lpnu.webhook.office.com/webhookb2/b298a7be-7ec9-4a23-aa5b-dbab38c1ed04@7631cd62-5187-4e15-8b8e-ef653e366e7a/JenkinsCI/aae6109ee7a8417d94c828ccc3cc2127/294e4ebb-5ec1-414e-8bf6-c622514c87e0/V2AluwY5OWDusU78Ettb7969tf-FSHMrKdIBdcs8ULU041', message: "⚠️ Unstable result for environment: ${params.DEPLOY_ENV}"
        }
    }
}
