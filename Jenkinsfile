def mvnHome
def remote = [:]
         remote.name = 'ansible'
         remote.host = '172.31.36.190'
         remote.user = 'centos'
         remote.password = 'Rnstech@123'
         remote.allowAnyHosts = true
def remote1 = [:]
    	remote1.name = 'kops'
    	remote1.host = '172.31.36.46'
    	remote1.user = 'centos'
    	remote1.password = 'Rnstech@123'
    	remote1.allowAnyHosts = true

pipeline {
    agent none
    
    stages{
        stage('Prepare Workspace') {
            steps {
                // Get src code from a GitHub repository
                git credentialsId: 'Github_un_pwd', url: 'https://github.com/venkat09docs/Maven-Java-Project.git'
                stash 'Source'
                script{
                    mvnHome = tool name: 'maven3', type: 'maven'
                }      
            }   
        }
        
        stage('Setup Tools'){
            steps{
            
                //sshCommand remote: remote, command: 'cd Maven-Java-Project; git pull'
                //sshCommand remote: remote, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/sonarqube/sonar-install.yaml'
                //sshCommand remote: remote, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/docker/docker-install.yml'

                //K8s Setup
                //sshCommand remote: remote1, command: "cd Maven-Java-Project; git pull"
                //sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/staging/namespace/staging-ns.yml"
                //sshCommand remote: remote1, command: "kubectl apply -f Maven-Java-Project/k8s-code/prod/namespace/prod-ns.yml"
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
