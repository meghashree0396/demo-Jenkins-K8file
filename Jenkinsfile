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
            kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "mykubeconfig3")
        }
      }
	      
		  }
 }
        
 
}
 def getVersion(){
 def  Commithash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return Commithash
}

		



