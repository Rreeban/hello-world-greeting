pipeline {

  agent any
  
 // agent {
 //   label 'Linux'
 // }

  stages {
  
    stage('Compilation') {
    
      steps {
      
        sh 'mvn -e -B -DskipTests clean package'
        
      }
      
    }
    
    stage('Analyse statique avec SonarQube') {
      
      steps {
        
        withSonarQubeEnv('SonarQube') {
          sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
        }

      }
      
    }
    
    stage('Test unitaire & publication') {
    
      steps {
      
        sh 'mvn test'
        
      }
      
      post {
      
        always {
        
          junit 'target/surefire-reports/*.xml'
          
        }
        
      }
      
    }
    
    stage ('Publication du binaire') {

      steps {

        sh "curl -u admin:Shaymin122 --upload-file target/*war 'http://84.39.42.17:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.war'"
        
      }

    }
    
  }
  
}
