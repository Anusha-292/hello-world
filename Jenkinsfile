pipeline {
  agent any 
  tools {
    maven 'maven'
  }
  stages {
    stage('Git Checkout'){
            steps {
               checkout scm
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
    
}
  }
