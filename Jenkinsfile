pipeline {
 environment{
    registry = "$DOCKER_REGISTRY"
    registryCredential = 'docker'
    SERVER="${WEB_SERVER}"
  }
  agent { docker { 
  image 'python:3.7.2'  
  args '--user 0:0' } }

  stages {

    stage('build') {
      steps {
        sh 'pip install -r requirements.txt'
        sh 'echo deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main >> /etc/apt/sources.list'
        sh 'apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367'
        sh 'apt update'
        sh 'apt install -y ansible'
        sh 'export ANSIBLE_HOST_KEY_CHECKING=False'
        sh 'echo "[webservers]" > /etc/ansible/hosts'
        sh 'echo $SERVER  >> /etc/ansible/hosts'
        sh 'cat /etc/ansible/hosts'
        sh 'pwd'      
        }
    }


    stage('test') {
      steps {
        sh 'python test.py'
      }
    }


stage ('Build Image') {

     when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
             steps{
        script {
          dockerImage = docker.build registry + ":latest"
        }
      }
    }
    
    stage('Deploy Image') {
      steps{
        script {
        
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'docker',
           usernameVariable: 'D_USERNAME', passwordVariable: 'D_PASSWORD']
           ]){
            ansiblePlaybook(playbook:'deployment/playbook.yml',
            credentialsId: 'web',
            disableHostKeyChecking: true,
            extraVars: [
                    D_USER: "${D_USERNAME}",
                    D_PASS: "${D_PASSWORD}"]
            )
          }
         
        }
      }
    }    
 }
}
}



