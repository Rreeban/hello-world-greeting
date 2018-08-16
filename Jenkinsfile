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
    
        stage('Tests & publication') {
    
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
        
        stage ('Validation de l\'application') {

          steps {

            script {

              def server = Artifactory.server('Artifactory')
              def uploadSpec = """{
                "files": [
                  {
                    "pattern": "/home/jenkins/tomcat/webapps/rondoudou.jar",
                    "target": "hel-lo_fiable/rondoudou_fiable${BUILD_NUMBER}.jar"
                  }
                ]
              }"""
              server.upload(uploadSpec)

            }

          }

        }

      }

    }

    stage (‘Déploiement’) {

      agent {
        label 'Production'
      }

      steps {

        script {

          def server = Artifactory.server('Artifactory')
          def downloadSpec = """{
            "files": [
              {
                "pattern": "hello_fiable/rondoudou_fibale${BUILD_NUMBER}.jar",
                "target": "/home/jenkins/tomcat/webapps/rondoudou_fiable.war"
              }
            ]
          }"""
          server.download(downloadSpec)

        }

      }

    }
            
  }
          
}
