def frontendImage="bartello88/frontend"
def backendImage="bartello88/backend"
def backendDockerTag=""
def frontendDockerTag=""
def dockerRegistry=""
def registryCredentials="dockerhub"


def dockerTag = ''

parameters {
  string 'backendDockerTag'
  string 'frontenddDockerTag'
}




pipeline {
    agent {
  label 'agent'
}
environment{
    PIP_BREAK_SYSTEM_PACKAGES=1
    scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
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
                scripts{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : 
                    params.backendDockerTag

                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : 
                    params.frontendDockerTag
                }
            }
        }
    }
}

