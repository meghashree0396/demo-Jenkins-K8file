#!/usr/bin/env groovy

pipeline {
 
 agent{
  node {
      label 'master'
    }
 }
 environment {
    DOCKER_TAG = getVersion()
}
 
 stages {
     
 
	stage('Initialize'){
	   steps{
		script{
			def dockerHome = tool 'myDocker'
			env.PATH = "${dockerHome}/bin:${env.PATH}"
		}
	  }
	}
	  
    stage('build') {
	  agent { docker { image 'python:3.8.5-alpine3.12' } }
      steps {
        sh '''
	      pip freeze > requirements.txt
	      pip install -r requirements.txt '''
      }
    }
	
    
	
    stage('Docker Image') {
      steps{
	      sh 'docker build -t 007892345/personal-python-test:${DOCKER_TAG} . '
		  }
        }
    stage('DockerHub Push') {
      steps{
          withCredentials([string(credentialsId: 'DockerHub1', variable: 'DockerHubPwd')]) {
         sh 'docker login -u 007892345 -p ${DockerHubPwd}'
}
	      sh 'docker push 007892345/personal-python-test:${DOCKER_TAG} '
		  }
        }
        stage('Deploy App') {
         steps {
          script {
            // kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "mykubeconfig1")
	       withKubeConfig([credentialsId: 'mykubeconfig0']) {
		       sh ' curl -LO "https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl" '
                       sh 'chmod u+x ./kubectl'
		       sh 'chmod +x changeTag.sh'
		       sh './changeTag.sh ${DOCKER_TAG}'
		       sh './kubectl apply -f deployment.yml'
    
}
        }
      }
	      
		  }
 }
        
 
}
 def getVersion(){
 def  Commithash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return Commithash
}

		



