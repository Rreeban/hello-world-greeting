pipeline {

  agent {
    label 'Linux'
  }

  stages {
  
    stage('Compilation') {
    
      steps {
      
        sh 'mvn -B -DskipTests clean package'
        
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

        script {

          def server = Artifactory.server('Artifactory')
          def uploadSpec = """{
            "files": [
              {
                "pattern": "target/*.war",
                "target": "depot_jenkins/rondoudou${BUILD_NUMBER}.jar"
              }
            ]
          }"""
          server.upload(uploadSpec)

        }

      }

    }
    
  }
  
}
