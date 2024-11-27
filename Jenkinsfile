def frontendImage="bobejoo/frontend"
def backendImage="bobejoo/backend"


pipeline {
    agent {
        label 'agent'
    }

    environment {
        PIP_BREAK_SYSTEM_PACKAGES = 1
    }
    
    tools {
        terraform 'Terraform'
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
        stage('docker rm synek') {
            steps {
                sh "docker rm -f frontend backend"
            }
        }
        stage('docker-compose up synek') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            sh "docker-compose up -d"
                    }
                }
            }
        }
        stage('Testy testy') {
            steps {
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest test/selenium/frontendTest.py"
            }
        }
        stage('Terraforma') {
            steps {
                dir('Terraform') {                
                    git branch: 'main', url: 'https://github.com/Bobejoo/Terraform'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                            sh 'terraform init -backend-config=bucket=kuba-es-panda-devops-core-19'
                            sh 'terraform apply -auto-approve -var bucket_name=kuba-es-panda-devops-core-19'
                            
                    } 
                }
            }
        }
    }
    post {
        always {
          sh "docker-compose down"
          cleanWs()
        }
    }
}
