#!/usr/bin/env groovy

pipeline {
   agent any
  
	   stages {
		
		//=========================================Start=============================================================
		
		/* cloning the repository to our workspace */
		stage ('Clone Repository'){
			steps{
			 checkout scm
			}
		}

		//=========================================Build Images ==============================================================
		
		stage('build Image') {
		  steps {
			sh 'pip install -r requirements.txt'
			sh 'docker build -t personal-python-test .'
		  }
		}
		
		//=========================================Run Image / Create Container ==============================================================
		stage('Run Image / Container Creation') {
		  steps {
			sh 'docker run -d --name myfirstcontainer personal-python-test'
		  }
		}
		
		//=========================================Test==============================================================
		stage('test') {
		  steps {
			sh 'python ./test.py'
		  }   
		}
			
		//=========================================End==============================================================
		
	  }
}