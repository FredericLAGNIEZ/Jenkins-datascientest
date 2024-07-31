
pipeline {

    agent any

    environment {
        DOCKER_ID = "FredericLAGNIEZ"
        DOCKER_IMAGE = "datascientestapi"
	DOCKER_TAG = "v.${BUILD_ID}.0"
    }


    stages {
        stage ('building') {
            steps {
	        sh pip install -r requirements.txt
            }
	}
            
	stage ('testing') {
            steps {
		 sh python -m unittest
            }
	 }

	stage ('deploying') {
	    steps {
	        script {
	            sh """
                    docker rm -f jenkins
                    docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                    docker run -d -p 8000:8000 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                }
	    }
        }
	
        stage ('User acceptance') {
	    input {
                message "Voulez-vous deployer le code sur la branche main?"
		ok "Yes"
	    }
	}

	stage ('Pushing and Merging') {
	    parallel {
                stage('Pushing') {
		    environment {
		        DOCKERHUB_CREDENTIALS = credentials('docker_jenkins') 
                    }
		    steps {
                        sh """
		        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
		        docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
		        """
                    }
                }

                stage('Merging') {
		    steps {
		        sh 'echo "Merging done"'
		    }
	        }
	}
	}
	}	 
    
    post {
        always {
            sh 'docker logout'
	}
    }        
}

    }
}
