pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        NEXUS_URL  = "172.31.34.232:8080"
        IMAGE_URL_WITH_TAG = "suizhidaidev/node-app:${DOCKER_TAG}"
    }
    stages{
	stage('SCM - Checkout'){
		steps{
			git url: 'https://github.com/NKStupid/node-app',
            		branch: 'joseph'
		}
	}
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t ${IMAGE_URL_WITH_TAG}"
            }
        }
        stage('Dockerhub Push'){
            steps{
		    
                withCredentials([string(credentialsId: 'suizhidaidev', variable: 'dockerhubPwd')]) {
                    sh "docker login -u suizhidaidev -p ${dockerhubPwd} "
                    sh "docker push ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
        /* stage('Docker Deploy Dev'){
            steps{
                sshagent(['tomcat-dev']) {
                    withCredentials([string(credentialsId: 'nexus-pwd', variable: 'nexusPwd')]) {
                        sh "ssh ec2-user@172.31.0.38 docker login -u admin -p ${nexusPwd} ${NEXUS_URL}"
                    }
					// Remove existing container, if container name does not exists still proceed with the build
					sh script: "ssh ec2-user@172.31.0.38 docker rm -f nodeapp",  returnStatus: true
                    
                    sh "ssh ec2-user@172.31.0.38 docker run -d -p 8080:8080 --name nodeapp ${IMAGE_URL_WITH_TAG}"
                }
            }
        } */
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
