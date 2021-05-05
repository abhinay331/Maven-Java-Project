pipeline {
    agent none
    
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
    }
}                    
