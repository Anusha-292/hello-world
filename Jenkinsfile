pipeline {
  agent any 
  tools {
    maven 'maven'
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

    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
            sh 'scp -o StrictHostKeyChecking=no target/*.war localhost:/opt/apache-tomcat-8.5.78/webapps/webapp.war'
              }      
           }       
    
}
  }
