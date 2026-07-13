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
                    selector: specific('7'),
                    filter: 'artifacts/*.tar.gz',
                    fingerprintArtifacts: true
                )


                sh '''
                    echo "Downloaded files:"
                    ls -lh artifacts
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
