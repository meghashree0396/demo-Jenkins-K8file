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
	     // sh 'docker build -t 007892345/personal-python-test:${DOCKER_TAG} . '
	        sh 'docker build -t test/personal-python-test:${DOCKER_TAG} . '
		  }
        }
    stage('DockerHub Push') {
      steps{
       //  withCredentials([string(credentialsId: 'DockerHub1', variable: 'harbor_cred')]) {
        // sh 'docker login -u 007892345 -p ${DockerHubPwd}'
	       // withCredentials([string( variable: 'harbor_auth')]) {
		//  sh 'docker login -u meghashree.munikrishna@ericsson.com -p ${harbor_auth}'
	     // sh '''echo $HARBOR_CREDENTIAL_PSW | docker login $REGISTRY -u 'meghashree.munikrishna@ericsson.com' -p ${harbor_auth}'''
			//sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME'
	      withCredentials([usernamePassword(credentialsId: 'harbor_cred', passwordVariable: 'Harbor_passwd', usernameVariable: 'Harbor_user')])
	     sh ' docker login -u ${Harbor_user} -p ${Harbor_passwd} hadoop-c04n06.ss.sw.ericsson.se:31333 '
	      sh 'docker tag test/personal-python-test:${DOCKER_TAG} hadoop-c04n06.ss.sw.ericsson.se:31333/test/test/personal-python-test:${DOCKER_TAG}'
	      sh 'docker push hadoop-c04n06.ss.sw.ericsson.se:31333/test/test/personal-python-test:${DOCKER_TAG}'
}
	         // sh 'docker push test/personal-python-test:${DOCKER_TAG} '
	    
		//  }
        }
        stage('Deploy App') {
         steps {
          script {
            // kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "mykubeconfig1")
	       withKubeConfig([credentialsId: 'mykubeconfig1']) {
		       sh ' curl -LO "https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl" '
                       sh 'chmod u+x ./kubectl'
		       sh '''final_tag=$(echo $DOCKER_TAG | tr -d ' ')
		       sed -i "s/DOCKER_TAG/$final_tag/g" deployment.yml'''
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

		



