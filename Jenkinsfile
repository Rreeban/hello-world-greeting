pipeline {

  agent {
    label 'agent_java'
  }

  stages {
     
    stage('Tests unitaires') {
    
      steps {
        sh 'mvn test'
      }
  
    }
    
    stage('Analyse statique') {
      
      steps {

        withSonarQubeEnv('SonarQube') {
          sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
        }

      }
      
    }
    
    stage('Compilation') {
    
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
      
    }
    
    stage ('Publication du binaire') {

      steps {
        sh "curl -u admin:password --upload-file target/*war 'http://http://84.39.47.231:8081/repository/depot_hello/rondoudou${BUILD_NUMBER}.war'"
      }

    }
    
  }
  
}
