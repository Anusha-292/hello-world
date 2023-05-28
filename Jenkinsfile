pipeline {
  agent any 
  tools {
    maven 'maven'
  }
  
  //stages {
   // stage('Git Checkout'){
       //     steps {
          //     checkout scm
           //  }
      //  }
    
    stage('UNIT Testing'){
        steps{
            script {
                sh 'mvn test'
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
    
}
  }
