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

        nexusPublisher nexusInstanceId: 'Nexus_jenkins', nexusRepositoryId: 'depot_maven_test', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/*.war']], mavenCoordinate: [artifactId: 'rondoudou', groupId: 'test', packaging: 'pok', version: '1.09']]]

      }

    }
    
  }
  
}
