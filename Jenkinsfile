def frontendImage="bartello88/frontend"
def backendImage="bartello88/backend"
def backendDockerTag=""
def frontendDockerTag=""
def dockerRegistry=""
def registryCredentials="dockerhub"


def dockerTag = ''


pipeline {
    agent {
  label 'agent'
}
environment{
    PIP_BREAK_SYSTEM_PACKAGES=1
    scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
}
parameters {
  string 'backendDockerTag'
  string 'frontendDockerTag'
}

    stages {
        stage('Get Code') {
            steps {
                checkout scm
            }
            
        }
      stage('Delete containers') {
          steps {
              sh "docker rm -f frontend backend"
          }
          
      }
        stage('Adjust Version'){
            steps{
                script{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : params.backendDockerTag
                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : params.frontendDockerTag
                    
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
        }
    }
      stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                       docker.withRegistry("$dockerRegistry", "$registryCredentials") {
                            sh "docker-compose up -d"
                        }
                    }
                }
            }
        }
        stage('Run tests') {
          steps {
              sh "pip3 install -r test/selenium/requirements.txt"
              sh "python3 -m pytest test/selenium/frontendTest.py"
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

