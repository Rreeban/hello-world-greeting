pipeline {

  agent none

  stages {
    
    stage('Compilation et tests') {
      
      agent {
        label 'Linux'
      }
      
      stages {
        
        stage('Compilation') {
    
          steps {
            //sh 'mvn -B -DskipTests clean package'
            sh 'mvn clean verify -DskipITs=true';
            junit '**/target/surefire-reports/TESTS-*.xml'
            archive 'target/*.jar'
          }
      
        }
    
        stage('Analyse statique avec SonarQube') {
      
          steps {
        
            withSonarQubeEnv('SonarQube') {
              sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
            }

          }
      
        }
    
        //stage('Test unitaire & publication') {
    
          //steps {
            //sh 'mvn test'
          //}
      
          //post {
      
            //always {
              //junit 'target/surefire-reports/*.xml'
            //}
        
          //}
      
        //}
    
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
    
    stage('Tests de déploiement') {
      
      agent {
        label 'Tomcat'
      }
      
      stages {
        
        stage ('Téléchargement du binaire') {
    
          steps {
      
            script {
        
              def server = Artifactory.server('Artifactory')
              def downloadSpec = """{
                "files": [
                  {
                    "pattern": "depot_jenkins/rondoudou${BUILD_NUMBER}.jar",
                    "target": "/home/jenkins/tomcat/webapps/rondoudou.jar"
                  }
                ]
              }"""
              server.download(downloadSpec)
       
            }
        
          }
    
        }
    
        stage ('test de performance') {
      
          steps {
            sh '/home/jenkins/jmeter/bin/jmeter.sh -n -t $WORKSPACE/pt/jmeter.jmx -l /home/jenkins/test_report.jtl'
          }
      
        }
    
        stage ('Deploiement') {
      
          steps {
      
            script {
        
              //Télécharger le binaire sur une autre machine.
              withCredentials([usernameColonPassword(credentialsId: 'artifactory', variable: 'credentials')]) {
                sh 'curl -u${credentials} -X PUT "http://84.39.47.48:8081/artifactory/api/storage/example-project/${BUILD_NUMBER}/hello-0.0.1.war?properties=Performance-Tested=Yes"';
              }
        
            }
          
          }
      
        }
    
      }
  
    }
    
  }
  
}
