pipeline {

  environment {

    registry = "dukenayak/demoproject"

    registryCredential = 'docker-creds'

    dockerImage = ''

  }

	agent {
        label 'master'
    }
    stages {
        stage("Fetch repository") {

      steps {

        git 'https://github.com/dukenayak/Docker-Jenkins-Demo'

      }

    }

    stage('Building image') {

      steps{

        script {

          dockerImage = docker.build registry + ":$BUILD_NUMBER"

        }

      }

    }

    stage('Deploy Image') {

      steps{

        script {

          docker.withRegistry( '', registryCredential ) {

            dockerImage.push()

          }

        }

      }

    }
	stage('Remove Existing Container') {

      steps{
	  script {
			sh '''

			a="$(docker container ls --format="{{.ID}}\t{{.Ports}}" | grep "8000" | awk '{print $1}')"

			echo $a

			if [ -z "$a" ]
			then
			echo "do not delete"
			else
			docker rm -f $a
			fi

			'''
		}
      }

    }
    
    	stage('Run Docker Image in Lab') {

      steps{

        script {

		        sh "docker run -d -p 8000:8000 ${dockerImage.imageName()}"
        }

      }

	}
stage('Remove dangling docker images') {
      steps{
        script {
                sh "docker system prune --force --all"
        }
      }
    }
  }

}
