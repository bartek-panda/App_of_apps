def imageName = '192.168.44.44:8082/docker_registry/frontend'

def dockerTag = ''


pipeline {
    agent {
  label 'agent'
}
environment{
    PIP_BREAK_SYSTEM_PACKAGES=1
    scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
}

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
            
        }

    }
}
