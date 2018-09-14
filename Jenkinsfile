pipeline {
  
  agent {
    label 'docker_java'
  }
  
  stages {
    
    stage('Compilation') {
      
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
      
    }
      
    stage('Analyse statique') {
        
      steps {
          
        withSonarQubeEnv('SoanrQube') {
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
    
    stage('Publication du binaire') {
      
      steps {
        sh "curl -u admin:password --upload-file target/*war 'http://{URL_DU_SERVEUR_NEXUS}/repository/depot_test/rondoudou${BUILD_NUMBER}.war'"
      }
      
    }
    
  }
      
} 
