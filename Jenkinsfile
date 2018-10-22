pipeline {
  
  agent none
  
  stages {
    
    stage('Compilation et tests') {

      agent {
        label 'agent_java'
      }

      stages {
  
        stage('Tests unitaires') {
    
          steps {
            sh 'mvn test'
          }
      
        }

        stage('Compilation') {
    
          steps {  
            sh 'mvn -B -DskipTests clean package'
          }
      
        }
            
        stage('Publication du binaire') {

          steps {
            sh "curl -u admin:admin123 --upload-file target/*.war 'http://84.39.47.231:8081/repository/depot_hello/rondoudou${BUILD_NUMBER}.war'"        
          }

        }
    
      }
  
    }
    
    stage('Tests de déploiement') {
      
      agent {
        label 'agent_tomcat'
      }
      
      stages {
        
        stage('Téléchargement du binaire') {
          
          steps {
            sh "wget -P /home/jenkins/tomcat/webapps http://84.39.47.231:8081/repository/depot_hello/rondoudou${BUILD_NUMBER}.war"
            sh "mv /home/jenkins/tomcat/webapps/rondoudou${BUILD_NUMBER}.war /home/jenkins/tomcat/webapps/rondoudou.war"
          }
 
        }
        
        stage('Test de performance') {
          
          steps {
            sh '/home/jenkins/apache-jmeter/bin/jmeter.sh -n -t ./jmeter.jmx -l /home/jenkins/test_report.jtl'
          }
         
        }
        
        stage ('Validation de l\'application') {
  
          steps {
            sh "curl -u admin:admin123 --upload-file /home/jenkins/tomcat/webapps/rondoudou.war 'http://84.39.47.231:8081/repository/hello_fiable/rondoudou_fiable${BUILD_NUMBER}.war'"
          }
  
        }
        
      }
      
    }
    
    stage('Creation de l\'image') {
      
      agent {
        label 'agent_docker'
      }
      
      stages {
        
        stage('Téléchargement du binaire') {
          
          steps {
            sh 'wget -P /home/jenkins/docker/tomcat_app http://84.39.47.231:8081/repository/hello_fiable/rondoudou_fiable${BUILD_NUMBER}.war'
            sh 'mv /home/jenkins/docker/tomcat_app/rondoudou_fiable${BUILD_NUMBER}.war /home/jenkins/docker/tomcat_app/rondoudou.war'
          }
          
        }
          
        stage('Compilation de l\'image') {
      
          steps {
            sh 'docker build -t tomcat_app /home/jenkins/docker/tomcat_app'
          }
          
        }
        
        stage('Stockage de l\'image') {
              
          steps {
            sh "docker tag tomcat_app reeban/tomcat_app:${BUILD_NUMBER}"
            sh 'docker tag tomcat_app reeban/tomcat_app'
            sh 'docker login -u reeban -p Shaymin122'
            sh "docker push reeban/tomcat_app:${BUILD_NUMBER}"
            sh 'docker push reeban/tomcat_app'
          }
         
        }
      }
    }
  }
}
