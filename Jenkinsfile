currentBuild.displayName = "Spring-#"+currentBuild.number
    agent any
    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git url: 'https://github.com/mahmud255/springboot-demo.git', branch: 'master'
                // Change file permisson
                sh "chmod +x -R . "
                // Run shell script
                sh './gradlew build'
            }
        }
        stage('Docker build') {
            steps {
                sh 'docker version'
				sh 'docker build -t spring .'
				sh 'docker image list'
				sh 'docker tag spring mahmud255/spring:latest'
            }
        }
        stage ('Docker Push') {
            steps{
                withCredentials([string(credentialsId: 'docker_key', variable: 'PASSWORD')]) {
				sh 'docker login -u mahmud255 -p $PASSWORD'
				sh 'docker push mahmud255/spring:latest'
                }
            }
        }
        
        stage('Docker Deploy'){
            steps{
                sshagent(['ssh-key']) {
                    withCredentials([string(credentialsId: 'ssh-key', variable: 'PASSWORD')]) {
                        sh 'docker login -u mahmud255 -p $PASSWORD'
                        sh 'docker push mahmud255/spring:latest'
                        
                    }
					// Remove existing container, if container name does not exists still proceed with the build
					sh script: "ssh root@192.168.0.200 docker rm -f springboot",  returnStatus: true
                    
                    sh "ssh root@192.168.0.200 docker run -d -p 8080:8080 --name springboot ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
    }


def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
