pipeline {
  agent any 
  tools {
    maven 'maven'
  }
  stages {
        stage ('Check-Git-Secrets') {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
        echo 'running trufflehog to check project history for secrets'
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/Anusha-292/hello-world.git > trufflehog'
        sh 'cat trufflehog'
        }
      }
    }
    
   stage ('Source Composition Analysis') {
      steps {
         //sh 'rm owasp* || true'
         sh 'wget "https://github.com/Anusha-292/hello-world/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         //sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
	 sh 'cp  /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.html ${WORKSPACE}'
	 }
    }
    
   stage ('SAST') {
      steps {
          echo 'Testing source code for security bugs and vulnerabilities'
          //sh 'export sonar-scanner=/data/sonar-scanner-4.8.0.2856-linux/bin'
         // sh 'sonar-scanner -version' 
          //sh 'mvn -version'
         sh 'sudo /opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=devsecops -Dsonar.sources=. -Dsonar.host.url=http://10.159.174.198:9000 -Dsonar.token=sqa_433411677fdacd148c80cbb969e227e8b28b3923'
         //sh 'cat target/sonar/report-task.txt'
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
      //sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://34.93.3122:8090/webapp/'
        sh "`docker run -t owasp/zap2docker-stable zap-baseline.py -t http://10.159.174.198:9090/webapp` || true"
       }
    }
  }
    
    post {
      success {
        emailext body: "<b>Project: ${env.JOB_NAME}</b><br>Build Number: ${env.BUILD_NUMBER} <br>DevSecOps vulnerabilities testing is Successful",mimeType: 'text/html', subject: 'Vulnerabilities testing is Successful', to: 'bandi.anusha@ril.com', attachmentsPattern:'dependency-check-report.html,trufflehog.txt', attachLog: true
      }
      failure {
        emailext body: "<b>Project: ${env.JOB_NAME}</b> <br>Build Number: ${env.BUILD_NUMBER} ", to: 'bandi.anusha@ril.com', subject: "ERROR CI: Project name -> ${env.JOB_NAME}",attachLog: true
      }
    }
}
