pipeline {
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
        sh 'echo "[webservers] \n  web@192.168.2.21" > /etc/ansible/hosts'
        sh 'ls -l'      }
    }
    stage('test') {
      steps {
        sh 'python test.py'
      }
    }


stage ('Deploy') {

     when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
steps {
    ansiblePlaybook(playbook:'deployment/playbook.yml', 
        credentialsId: 'web',
         disableHostKeyChecking: true)
        



        }       
    }
   }
     }




