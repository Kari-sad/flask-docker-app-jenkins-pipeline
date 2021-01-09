pipeline {
    agent any
    environment {        
	DOCKER_HUB_REPO = "karinegh18/test"
	REGISTRY_CREDENTIAL = "dockerhub"
        CONTAINER_NAME = "flask-container"
        STUB_VALUE = "200"
    }
    stages {
        stage('Stubs-Replacement'){
            steps {
                // 'STUB_VALUE' Environment Variable declared in Jenkins Configuration 
                echo "STUB_VALUE = ${STUB_VALUE}"
                sh "sed -i 's/<STUB_VALUE>/$STUB_VALUE/g' config.py"
                sh 'cat config.py'
            }
        }
        stage('Build') {
            steps {
		    script {
			//  Building new image
			sh 'docker image build -t $DOCKER_HUB_REPO:latest .'
			sh 'docker image tag $DOCKER_HUB_REPO:latest $DOCKER_HUB_REPO:$BUILD_NUMBER'

			//  Pushing Image to Repository
			docker.withRegistry( '', REGISTRY_CREDENTIAL ) {
				sh 'docker push DOCKER_HUB_REPO:$BUILD_NUMBER'
				sh 'docker push DOCKER_HUB_REPO:latest'
			}
                
                	echo "Image built and pushed to repository"
		    }
	    }
        }
        stage('Deploy') {
            steps {
                script{
					if ( ! "$(docker ps -q -f name=$CONTAINER_NAME)" ); then
						if ( "$(docker ps -aq -f status=exited -f name=$CONTAINER_NAME)" ); then
							# cleanup
							sh 'docker stop $CONTAINER_NAME'
							sh 'docker rm $CONTAINER_NAME'
						fi
						# run container
						sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
					fi
                    //sh 'echo "Latest image/code deployed"'
                }
            }
        }
    }
}

