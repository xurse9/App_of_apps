def frontendImage = "xurse9/panda-frontend"
def backendImage = "xurse9/panda-backend" 

pipeline {
    agent {
      label 'agent'
    }
    parameters {
        string defaultValue: 'latest', description: 'test', name: 'backendDockerTag'
        string defaultValue: 'latest', description: 'test', name: 'frontendDockerTag'
    }
    stages {
        stage('Get code') {
            steps {
                checkout scm
            }
        }
        stage('Adjust version') {
            steps {
                script {
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend ${frontendDockerTag}"
                }
            }
        }
    }
}
