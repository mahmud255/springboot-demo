pipeline {
    agent any
    stages {
        stage ('Git Clone') {
            steps{
                git credentialsId: 'github_key', url: 'https://github.com/mahmud255/springboot-demo.git'
            }
        }
        
        stage ('Gradle Build') {
            steps{
                sh './gradlew build'
            }
        }
        
          stage ('Docker build') {
            steps{
                sh 'docker version'
				sh 'docker build -t jhooq-docker-demo .'
				sh 'docker image list'
				sh 'docker tag jhooq-docker-demo rahulwagh17/jhooq-docker-demo:jhooq-docker-demo'
            }
        }
        stage ('Docker Login') {
            steps{
                withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
					sh 'docker login -u mahmud255 -p $docker_key'
            }
        }  
        
         stage ('Push Image') {
            steps{
                sh 'docker push  rahulwagh17/jhooq-docker-demo:jhooq-docker-demo'
            }
        }
        
    }
}
}
