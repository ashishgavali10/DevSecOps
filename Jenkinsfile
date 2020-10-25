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
              sh 'rm trufflehog || true'
              sh 'docker run dxa4481/trufflehog --json https://github.com/ashishgavali10/DevSecOps.git > trufflehog'
              sh 'cat trufflehog'           
      }
    }
    stage('Software composition analysis'){
      steps{
            sh 'rm owasp* || true'
            sh 'wget https://raw.githubusercontent.com/ashishgavali10/DevSecOps/master/owasp-dependency-check.sh'
            sh 'chmod +x owasp-dependency-check.sh'
            sh 'sudo bash owasp-dependency-check.sh'
            sh 'cat /var/lib/jenkins/workspace/sample project/odc-reports/dependency-check-report.xml'
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
         sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.209.251.22:/home/ubuntu/workspace/apache-tomcat-8.5.59/webapps/webapp.war'
       }
     }
    }
  }
}
                  
