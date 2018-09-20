pipeline {
  
  agent none
  
  stages {
    
    stage('Compilation et tests') {
  
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
    
        stage('Publication du binaire') {
      
          steps {
            sh "curl -u admin:password --upload-file target/*war 'http://84.39.42.17:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.jar'" 
          }
      
        }
        
      }
      
    }
    
    stage('Tests de déploiement') {
      
      agent {
        label 'docker_tomcat'
      }
      
      stages {
        
        stage('Téléchargement du binaire') {
          
          steps {
            sh "wget -P /home/jenkins/tomcat/webapps http://84.39.42.17:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.jar"
            sh "mv /home/jenkins/tomcat/webapps/rondoudou${BUILD_NUMBER}.jar /home/jenkins/tomcat/webapps/rondoudou.war"
          }
          
        }
        
        stage('Test de performance') {
          
          steps {
            sh '/home/jenkins/apache-jmeter/bin/jmeter.sh -n -t $WORKSPACE/pt/jmeter.jmx -l /home/jenkins/test_report.jtl'
          }
          
        }
        
        stage('Validation de l\'application') {
          
          steps {
            sh "curl -u admin:password --upload-file /home/jenkins/tomcat/webapps/rondoudou.war 'http://84.39.42.17:8081/repository/hello_fiable/rondoudou_fiable${BUILD_NUMBER}.jar'"
            sh "curl -u admin:password --upload-file /home/jenkins/tomcat/webapps/rondoudou.war 'http://84.39.42.17:8081/repository/hello_livrable/dernier_rondoudou_fiable.jar'"
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
            sh 'wget -P /home/jenkins/docker/tomcat_app http://84.39.42.17:8081/repository/hello_livrable/dernier_rondoudou_fiable.jar'
            sh 'mv /home/jenkins/docker/tomcat_app/dernier_rondoudou_fiable.jar /home/jenkins/docker/tomcat_app/rondoudou.war'
          }
          
        }
            
        stage('Compilation de l\'image') {
      
          steps {
            sh 'docker build -t tomcat_app /home/jenkins/docker/tomcat_app'
          }
          
        }
        
        stage('Stockage de l\'image') {
              
          steps {
            sh "docker tag tomcat_app reeban/trez:${BUILD_NUMBER}"
            sh 'docker tag tomcat_app reeban/trez'
            sh 'docker login -u reeban -p password'
            sh "docker push reeban/trez:${BUILD_NUMBER}"
            sh 'docker push reeban/trez'
          }
              
        }
              
      }
              
    }
    
    stage('Déploiement') {
      
      agent {
        label 'production_docker'
      }
    
      stages {
        
        stage ('Mise en production') {
            
          steps {
                
            sh 'docker pull reeban/trez'
            sh 'docker rm -f test'
            sh 'docker run -d -p 8080:8080 --name="test" reeban/trez'
                
          }
    
        }

      }
    
    }
    
  }
  
}
