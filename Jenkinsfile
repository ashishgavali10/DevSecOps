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
           
      }
    }
    stage('SAST'){
      steps{
        withSonarQubeEnv('sonarqube'){
          sh 'mvn sonar:sonar'
        }
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
         sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@52.54.136.50:/home/ubuntu/workspace/apache-tomcat-8.5.59/webapps/webapp.war'
       }
     }
    }
    stage ('DAST') {
      steps {
        sshagent(['TomcatServer']){
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.54.136.50 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://52.54.136.50:8080/webapp/" || true'
        }
      }
    }
  }
}
                  
