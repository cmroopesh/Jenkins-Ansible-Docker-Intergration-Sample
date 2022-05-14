pipeline{
    agent any
    tools {
      maven 'maven3'
    }
    
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git branch: 'main', url: 'https://github.com/cmroopesh/Jenkins-Ansible-Docker-Intergration-Sample'
            }
        }

        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }

        stage('Docker Build'){
            steps{
                sh "docker build . -t docker3019/hariapp:${DOCKER_TAG} "
            }
        }

      
 stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u docker3019 -p ${dockerHubPwd}"
                }

                sh "docker push docker3019/hariapp:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'Deploy-docker.yml'
            }
        }
      
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
