pipeline {

  agent {
    label 'agent_java'
  }

  stages {
     
    stage('Tests unitaire') {
    
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
        sh "curl -u admin:Shaymin122 --upload-file target/*war 'http://84.39.43.46:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.war'"
      }

    }
    
  }
  
}
