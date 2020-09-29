pipeline {
    agent any
    stages {
		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}
		stage('Build Docker Image') {
			steps {
				sh 'docker build -t my-app .'
			}
		}
		stage('Push Docker Image') {
			steps {
				withAWS(region:'us-west-2',credentials:'CapstoneCredential') {
					sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 430721128257.dkr.ecr.us-west-2.amazonaws.com'  
					sh 'docker build -t mayank/my-app .'
					sh 'docker tag mayank/my-app:latest 430721128257.dkr.ecr.us-west-2.amazonaws.com/mayank-repo'
					sh 'docker push 430721128257.dkr.ecr.us-west-2.amazonaws.com/mayank-repo'
				}
			}
		}
		stage('Create Kube Config') {
			steps {
				withAWS(region:'us-west-2',credentials:'CapstoneCredential') {
					sh 'aws eks --region us-west-2 update-kubeconfig --name mayank-cluster'  
				}
			}
		}
		stage('Deploy Containers') {
			steps {
				withAWS(region:'us-west-2',credentials:'CapstoneCredential') {
					sh 'kubectl apply -f deployment.yaml'  
					sh 'kubectl apply -f services.yaml'
				}
			}
		}       
	}
}  