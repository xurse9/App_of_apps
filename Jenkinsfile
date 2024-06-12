def frontendImage = "xurse9/panda-training"
def backendImage = "xurse9/panda-training" 

pipeline {
    agent {
      label 'agent'
    }

    parameters {
    string defaultValue: 'panda_back', description: 'test', name: 'backendDockerTag'
    string defaultValue: 'panda_front', description: 'test', name: 'frontendDockerTag'
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
                    currentBuild.description = "Backend: ${}, Frontend ${frontendDockerTag}"
                }
            }
        }
    }
}
