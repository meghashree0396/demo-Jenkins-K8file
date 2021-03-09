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
          withCredentials([string(credentialsId: 'DockerHub', variable: 'DockerHubPwd')]) {
         sh 'docker login -u 007892345 -p ${DockerHubPwd}'
}
	      sh 'docker push 007892345/personal-python-test:${DOCKER_TAG} '
		  }
        }
        stage('Ansible Playbook') {
      steps{
         script{
           sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
           sed -i "s/Docker_tag/$final_tag/g" deployment.yml
           '''
           ansiblePlaybook become: true , installation: 'ansible', playbook: 'playbook.yml'
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

		



