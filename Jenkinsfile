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
    tools {
        terraform 'Terraform'
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
        //     stage('Selenium tests') {
        //     steps {
        //         sh "pip3 install -r test/selenium/requirements.txt"
        //         sh "python3 -m pytest test/selenium/frontendTest.py"
        //     }
        // }

        stage('Run terraform') {
            steps {
                dir('Terraform') {                
                    git branch: 'main', url: 'https://github.com/bartek-panda/Terraform.git'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                            sh 'terraform init -backend-config=bucket=bartlomiej-zdunek-panda-devops-core-16'
                            sh 'terraform apply -auto-approve -var bucket_name=bartlomiej-zdunek-panda-devops-core-16'
                            
                    } 
                }
            }
        }
        stage('Run Ansible') {
               steps {
                   script {
                        sh "pip3 install -r requirements.txt"
                        sh "ansible-galaxy install -r requirements.yml"
                        withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                                 "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            ansiblePlaybook inventory: 'inventory', playbook: 'playbook.yml'
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

