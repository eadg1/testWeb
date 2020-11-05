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
        sh 'pwd'      }
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

    def customImage = docker.build("my-image:${env.BUILD_ID}")
    customImage.push()

        }
    }
  }
}




