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
			git url: 'https://github.com/NKStupid/node-app'
            		//branch: 'joseph'
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
	stage('Deploy to k8s'){
		steps{
			sh "chmod +x changeTag.sh"
			sh "./changeTag.sh ${DOCKER_TAG}"
			sshagent(['kubeClient']) {
			    // some block                    
			sh """
				scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml chenkiegcp2@i3.chenkiegcp2.chensiyi.dev:/home/chenkiegcp2/kube-manifest
				ssh chenkiegcp2@i3.chenkiegcp2.chensiyi.dev kubectl apply -f /home/chenkiegcp2/kube-manifest/
			    """
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
