pipeline {

    agent any


    environment {

        SERVICE_VERSION = "1.0.0"

    }


    stages {


        stage('Download Artifact') {

            steps {

                echo "Download artifact from Pipeline A"


                copyArtifacts(
                    projectName: 'pipeline_compile',
                    selector: lastSuccessful(),
                    filter: 'artifacts/*.tar.gz',
                    fingerprintArtifacts: true
                )


                sh '''
                    echo "Downloaded files:"
                    ls -lh artifacts
                '''

            }
        }


	stage('Stop Old Service') {

	    steps {

		sh '''
		set +e

		APP=/var/jenkins_home/devops/test-env/enterprise-service-1.0.0

		if [ -f "$APP/scripts/stop.sh" ]; then

		    echo "Stopping old service"

		    chmod +x $APP/scripts/stop.sh

		    $APP/scripts/stop.sh

		else

		    echo "No old service"

		fi
		'''
	    }
	}


	stage('Deploy Test Environment') {

	    steps {

		echo "Deploy artifact to test environment"


		sh '''
		set -e


		rm -rf ~/devops/test-env/*


		mkdir -p ~/devops/test-env


		tar xzf artifacts/enterprise-service-1.0.0.tar.gz \
		    -C ~/devops/test-env


		ls -R ~/devops/test-env

		'''

	    }
	}


	stage('Start Test Service') {

	steps {

	sh '''

	cd /var/jenkins_home/devops/test-env/enterprise-service-1.0.0

	chmod +x scripts/start.sh

	scripts/start.sh


	sleep 3


	cat service.log

	'''

	}

	}

	stage('Health Check') {

	    steps {

		echo "Check enterprise service health"


		sh '''

		cd /var/jenkins_home/devops/test-env/enterprise-service-1.0.0


		chmod +x scripts/health_check.sh


		./scripts/health_check.sh

		'''

	    }
	}


    }


    post {

        success {

            echo "Pipeline B SUCCESS"

        }


        failure {

            echo "Pipeline B FAILED"

        }

    }

}
