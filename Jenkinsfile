pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
           ''' 
      }
    }
    stage ('Check git secrets'){
      steps{
        sh '''
              rm trufflehog || true
              docker run dxa4481/trufflehog --json https://github.com/ashishgavali10/DevSecOps.git > trufflehog
              cat trufflehog
           '''
      }
    }
    stage ('Build') {
      steps {
        sh 'mvn clean package'
      }
    }
    stage ('Deploy to tomcat') {
     steps {
       sshagent(['TomcatServer']) {
         sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@34.233.124.251:/home/ubuntu/workspace/apache-tomcat-8.5.59/webapps/webapp.war'
       }
     }
    }
  }
}
                  
