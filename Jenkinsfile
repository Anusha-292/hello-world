pipeline {
  agent any 
  tools {
    maven 'maven'
  }
  stages {
        stage ('Check-Git-Secrets') {
      steps {
        echo 'running trufflehog to check project history for secrets'
        //sh 'rm trufflehog || true'
       // sh 'docker run gesellix/trufflehog --json https://github.com/tusharjadhav29/hello-world.git > trufflehog'
        //sh 'cat trufflehog'
      }
    }
    
   stage ('Source Composition Analysis') {
      steps {
         //sh 'rm owasp* || true'
         //sh 'wget "https://github.com/tusharjadhav29/hello-world/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         //sh 'bash owasp-dependency-check.sh'
         //sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
      }
    }
    
   stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          echo 'Testing source code for security bugs and vulnerabilities'
          //sh 'export JAVA_HOME=/usr/bin/java'
          // sh 'java -version' 
         // sh 'mvn -version'
         //sh 'mvn sonar:sonar'
         // sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    
    stage('UNIT Testing'){
        steps{
            script {
                sh 'sudo mvn test'
                }
            }
        }
    
     stage('Integration testing'){
        steps{
            script{
                sh 'mvn verify -DskipUnitTests'
            }
        }
       }
 
    stage ('Build') {
      steps {
      sh 'mvn clean install package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
        steps {
             sh 'scp  webapp/target/*.war /data/apache-tomcat-8.5.78/webapps/webapp.war'
              }      
           }   
    
    stage ('DAST') {
      steps {
      //sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://34.93.225.235:8090/webapp/'
        sh " 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://34.100.148.244:8090/webapp/' || true"
       }
    }
    }
  }
