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
        sh 'docker run gesellix/trufflehog --json https://github.com/tusharjadhav29/hello-world.git > trufflehog'
        sh 'cat trufflehog'
        }
      }
    }
    
   stage ('Source Composition Analysis') {
      steps {
         //sh 'rm owasp* || true'
         //sh 'wget "https://github.com/tusharjadhav29/hello-world/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
      }
    }
    
   stage ('SAST') {
      steps {
          echo 'Testing source code for security bugs and vulnerabilities'
          //sh 'export sonar-scanner=/data/sonar-scanner-4.8.0.2856-linux/bin'
         // sh 'sonar-scanner -version' 
          //sh 'mvn -version'
         sh 'sudo /data/sonar-scanner-4.8.0.2856-linux/bin/sonar-scanner -Dsonar.projectKey=devsecops -Dsonar.sources=. -Dsonar.host.url=http://34.93.31.22:9000 -Dsonar.token=sqa_2810d9cd6a44cf8f277282e3536d8f300738f6b3'
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
        sh "`docker run -t owasp/zap2docker-stable zap-baseline.py -t http://34.93.31.22:8090/webapp` || true"
       }
    }
  }
    
    post {
      always {
        emailext body: 'Deployment is being started', subject: 'Email confirmation', to: 'newrelic29@gmail.com'
      }
      success {
        emailext body: readFile("/server/target/surefire-reports/com.example.TestGreeter.txt"),mimeType: 'text/html', subject: 'Email confirmation', to: 'newrelic29@gmail.com'
      }
      failure {
        emailext body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL build: ${env.BUILD_URL}", to: 'newrelic29@gmail.com', subject: "ERROR CI: Project name -> ${env.JOB_NAME}"
      }
    }
}
