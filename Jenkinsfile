def remote = [:]
         remote.name = 'ansible'
         remote.host = '172.31.7.119'
         remote.user = 'root'
         remote.password = 'root'
         remote.allowAnyHosts = true
def remote1 = [:]
    	remote1.name = 'kops'
    	remote1.host = '172.31.6.45'
    	remote1.user = 'root'
    	remote1.password = 'root'
    	remote1.allowAnyHosts = true
pipeline {
    agent { label 'slave'}

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven38"
    }

    stages {
        stage('Prepare-Workspace') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'github', url: 'https://github.com/abhinay331/Maven-Java-Project.git'    
		stash 'Source'
            }
        stage('Setup Tools'){
            steps{
                sshCommand remote: ansible, command: 'cd Maven-Java-Project; git pull'
                sshCommand remote: ansible, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/sonarqube/sonar-install.yaml'
                sshCommand remote: ansible, command: 'cd Maven-Java-Project; ansible-playbook -i hosts tools/docker/docker-install.yml'

                //K8s Setup
                sshCommand remote: kops, command: "cd Maven-Java-Project; git pull"
	        sshCommand remote: kops, command: "kubectl apply -f Maven-Java-Project/k8s-code/staging/namespace/staging-ns.yml"
    	        sshCommand remote: kops, command: "kubectl apply -f Maven-Java-Project/k8s-code/prod/namespace/prod-ns.yml"            
                }
            }
        }
    }
}
