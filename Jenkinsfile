pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'sincere-blade-314120'
                CLUSTER_NAME = 'sayilganman-k8s'
                LOCATION = 'us-central1-c'
                CREDENTIALS_ID = 'sincere-blade-314120'		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'
                }
           }
	    
	   stage('Test') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
		   sh 'whoami'
                   script {
		      myimage = docker.build("mustafasayilgan/devops:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
                      docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                      myimage.push("${env.BUILD_ID}")
                     }   
                   }
                }
            }

	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
                   sh "gcloud container clusters get-credentials sayilganman-k8s --zone us-central1-c --project sincere-blade-314120"
                   sh "gcloud --quiet config set project ${PROJECT_ID}"
                   sh "gcloud --quiet config set compute/zone ${LOCATION}"
                   sh "gcloud config set project jenkins-deploy@sincere-blade-314120.iam.gserviceaccount.com"
                   sh "kubectl apply -f deployment.yaml"
		   echo "Deployment Finished ..."
            }
	   }
    }
}
