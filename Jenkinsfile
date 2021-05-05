pipeline {
    agent {
        label 'slave'
    }
    stages{
        stage('Prepare Workspace') {
            steps {
                // Get src code from a GitHub repository
                git credentialsId: 'github', url: 'https://github.com/abhinay331/Maven-Java-Project.git'
                stash 'Source'
                script{
                    mvnHome = tool name: 'maven38', type: 'maven'
                }      
            }   
        }
        stage('SonarQube analysis') {
            steps{
                echo "Sonar Scanner"
                sh "mvn clean compile"
                withSonarQubeEnv('sonar-7.4') { 
                    sh "mvn sonar:sonar "
                } 
            }
        }
        
      stage('Unit Test Cases') {

          steps{
              sh "mvn clean test"  
          }
          post{
              success{
                  junit 'target/surefire-reports/*.xml'
              }
          }
      }
       stage('Build Code') {
          steps{
	      unstash 'Source'
              sh "mvn clean package"  
          }
          post{
              success{
                  archiveArtifacts '**/*.war'
              }
          }
      } 
    stage('Build Docker Image') {
         agent {
                  label 'Slave'
         }
         steps{
                  sh "docker build -t abhinay331/mainproject ."  
         }
     }
     
     stage('Publish Docker Image') {
         agent {
                  label 'Slave'
         }
    steps{

    	withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
    		sh "docker login -u ${dockerUser} -p ${dockerPassword}"
	}
        	sh "docker push abhinay331/mainproject"
    }
    }
    }
}                    
