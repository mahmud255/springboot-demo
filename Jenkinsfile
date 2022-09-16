currentBuild.displayName = "Spring-#"+currentBuild.number
pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        NEXUS_URL = "https://github.com/mahmud255/springboot-demo.git"
        IMAGE_URL_WITH_TAG = "${NEXUS_URL}/springboot:${DOCKER_TAG}"
    }
    
    stages{
        stage('Build Docker'){
            steps{
                sh "docker build -t ${IMAGE_URL_WITH_TAG} ."
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'docker_key', variable: 'PASSWORD')]) {
                    sh "docker login -u mahmud255 -p ${PASSWORD} ${NEXUS_URL}"
                    sh "docker push ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
        stage('Docker Deploy'){
            steps{
                sshagent(['ssh-key']) {
                    withCredentials([string(credentialsId: 'ssh-key', variable: 'PASSWORD')]) {
                        sh "ssh root@192.168.0.200 docker login -u mahmud255 -p ${PASSWORD} ${NEXUS_URL}"
                    }
					// Remove existing container, if container name does not exists still proceed with the build
					sh script: "ssh root@192.168.0.200 docker rm -f springboot",  returnStatus: true
                    
                    sh "ssh root@192.168.0.200 docker run -d -p 8080:8080 --name springboot ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
