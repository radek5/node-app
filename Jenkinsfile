pipeline {
    agent any
  
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t kammana/nodeapp:latest"
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker_login', passwordVariable: 'pass', usernameVariable: 'user')])
		    sh "docker login -u ${user} -p ${pass}"
                    sh "docker push kammana/nodeapp:latest"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
		sshagent(['k8s-master']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@3.8.153.242:/home/ec2-user/nodeapp"
			script{
			    try{
				sh "ssh ec2-user@3.8.153.242 kubectl apply -f ."
			    }catch(error){
			    	sh "ssh ec2-user@3.8.153.242 kubectl create -f ."
			    }
		        }
		}
          }
     }
}


def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
