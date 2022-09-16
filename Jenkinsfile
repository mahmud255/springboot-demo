currentBuild.displayName = "Spring Boot Application-#"+currentBuild.number

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
				sh 'docker build -t k8-integration .'
				sh 'docker image list'
				sh 'docker tag k8-integration mahmud255/k8-integration:latest'
            }
        }
        stage ('Docker Login') {
            steps{
                withCredentials([string(credentialsId: 'docker_key', variable: 'PASSWORD')]) {
				sh 'docker login -u mahmud255 -p $PASSWORD'
                }
            }
        }
        stage ('Push Image') {
            steps{
                sh 'docker push mahmud255/k8-integration:latest'
            }
        }
        
        stage("SSH Into k8s Server") {
            steps{
                script {
                    def remote = [:]
                    remote.name = 'K8S master'
                    remote.host = '192.168.0.200'
                    remote.user = 'root'
                    remote.password = 'root123'
                    remote.allowAnyHosts = true
                }  
                echo 'Login Succeeded'
            } 
        }     
       
        stage('Put deploymentFile onto Cluster') {
            steps{
                script {
                   { context ->
                        sshPut remote: remote, from: 'k8s-spring-boot-deployment.yml', into: '.'
                   }
                }
            }
        }
        stage('Deploy') {
            steps{
                script {
                    { context ->
                        sshCommand remote: remote, command: "kubectl apply -f k8s-spring-boot-deployment.yml"
                    }
                }
            }
        }
       
       
        
    }
}
