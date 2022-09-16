currentBuild.displayName = "Spring-#"+currentBuild.number
pipeline {
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
        
        stage('Deploy'){
            steps{
                sshagent(['ssh-key']) {
                sh "scp -o StrictHostKeyChecking=no k8s-spring-boot-deployment.yml root@192.168.0.200:/root/deploy/"
                script{
                    try{
                        sh "ssh root@192.168.0.200 kubectl apply -f ."
                }catch(error){
                        sh "ssh root@192.168.0.200 kubectl create -f ."
                    }
                }
            }
        }
        }
    }
}
