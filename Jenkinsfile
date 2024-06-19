def frontendImage = "xurse9/panda-frontend"
def backendImage = "xurse9/panda-backend" 

pipeline {
    agent {
      label 'agent'
    }
    parameters {
        string defaultValue: 'latest', description: 'description', name: 'backendDockerTag'
        string defaultValue: 'latest', description: 'description', name: 'frontendDockerTag'
    }
    environment {
        PIP_BREAK_SYSTEM_PACKAGES = 1
    }
    tools {
        terraform 'Terraform'
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
        stage('Clean running containers') {
            steps {
                sh "docker rm -f frontend backend"
            }
        }
        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            sh "docker-compose up -d"
                    }
                }
            }
        }
        stage('Selenium tests') {
            steps {
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest test/selenium/frontendTest.py"
            }
        }
        stage('Run terraform') {
            steps {
                dir('Terraform') {                
                    git branch: 'master', url: 'https://github.com/xurse9/Terraform'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                        sh 'terraform init -backend-config=bucket=mateusz-niewitala-panda-devops-core-18'
                        sh 'terraform apply -auto-approve -var bucket_name=mateusz-niewitala-panda-devops-core-18'    
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
    }
    post {
        always {
          sh "docker-compose down"
          cleanWs()
        }
    }
}