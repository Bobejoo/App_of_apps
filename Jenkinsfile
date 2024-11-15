def frontendImage="bobejoo/frontend"
def backendImage="bobejoo/backend"


pipeline {
    agent {
        label 'agent'
    }

      parameters {
        string(name: 'backendDockerTag', defaultValue: 'latest', description: 'back image tag desc')
        string(name: 'frontendDockerTag', defaultValue: 'latest', description: 'front image tag desc')
    }

    stages {
        stage('Get Repo') {
            steps {
                checkout scm
            }
        }
        stage('Adjust ver') {
            steps {
                script{
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }
        }
    }
}
