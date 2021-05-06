def remote1 = [:]
    	remote1.name = 'kops'
    	remote1.host = '172.31.6.45'
    	remote1.user = 'ec2-user'
    	remote1.password = 'root'
    	remote1.allowAnyHosts = true

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
      stage('Setup Tools'){
    
          steps{
          
            //sshCommand remote: remote, command: 'cd Maven-Java-Project; git pull'
            //sshCommand remote: remote, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/sonarqube/sonar-install.yaml'
            //sshCommand remote: remote, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/docker/docker-install.yml'

            //K8s Setup
            sshCommand remote: remote1, command: "cd Maven-Java-Project; git pull"
	    sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/staging/namespace/staging-ns.yml"
	    sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/prod/namespace/prod-ns.yml"
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

         steps{
                  sh "docker build -t abhinay331/mainproject ."  
         }
     }
     
     stage('Publish Docker Image') {

    steps{

    	withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
    		sh "docker login -u ${dockerUser} -p ${dockerPassword}"
	}
        	sh "docker push abhinay331/mainproject"
    }
    }
    stage('Deploy to Staging') {
	
	steps{
	      //Deploy to K8s Cluster 
              echo "Deploy to Staging Server"
	      sshCommand remote: remote1, command: "cd Maven-Java-Project; git pull"
	      sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/staging/app/."
	}		    
    }
    stage ('Integration-Test') {
	
	steps {
             echo "Run Integration Test Cases"
             unstash 'Source'
             sh "mvn clean verify"
        }
    }
    stage ('approve') {
	
	steps {
		echo "Approval State"
                timeout(time: 7, unit: 'DAYS') {                    
			input message: 'Do you want to deploy?', submitter: 'admin'
		}
	}
     }	
    stage ('Prod-Deploy') {
	
	steps{
              echo "Deploy to Production"
	      //Deploy to Prod K8s Cluster
	      sshCommand remote: remote1, command: "cd Maven-Java-Project; git pull"
	      sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/prod/app/."
	}
	}	    
    }
}                    
