pipeline {
   agent any
   environment {
       registry = "zerocoolbr/capstonedevops"
   }
   stages {
       stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}

       stage('Build') {
           steps {
               sh 'docker build -t zerocoolbr/capstonedevops .'
           }
       }

       stage('Publish') {
           environment {
               registryCredential = 'dockerhub'
           }
           steps{
               script {
                   def appimage = docker.build registry + ":$BUILD_NUMBER"
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push()
                       appimage.push('latest')
                   }
               }
           }
       }
       stage ('Set kubectl context') {
           steps {
               withAWS(region:'us-east-1', credentials:'jenkins-aws') {
			        sh '''
						kubectl config use-context arn:aws:eks:us-east-1:413842618588:cluster/capstone-cluster-udacity
					'''
                }
           }
       }

       stage('Deploy blue container') {
			steps {
				withAWS(region:'us-east-1', credentials:'jenkins-aws') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}

        stage('Deploy green container') {
			steps {
				withAWS(region:'us-east-1', credentials:'jenkins-aws') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}

		stage('Create the service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'us-east-1', credentials:'jenkins-aws') {
					sh '''
						kubectl apply -f ./blue-service.json
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the service in the cluster, redirect to green') {
			steps {
				withAWS(region:'us-east-1', credentials:'jenkins-aws') {
					sh '''
						kubectl apply -f ./green-service.json
					'''
				}
			}
		}
   }
}
  