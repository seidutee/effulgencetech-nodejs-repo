
//def COLOR_MAP = [
//    'SUCCESS': 'good', 
//    'FAILURE': 'danger',
//]

pipeline{

	agent any

	//rename the user name "seidut" with the username of your dockerhub repo.
	environment {
		DOCKERHUB_CREDENTIALS=credentials('DOCKERHUB_CREDENTIALS')
		IMAGE_REPO_NAME = "seidut/effulgencetech-nodejs-img"
		CONTAINER_NAME= "effulgencetech-nodejs-cont-"
	}
	
//Downloading files into repo
	stages {
		stage('Git checkout') {
            		steps {
                		echo 'Cloning project codebase...'
                		git branch: 'main', url: 'https://github.com/seidutee/effulgencetech-nodejs-repo.git'
            		}
        	}
	
//Building and tagging our Docker image

		stage('Build-Image') {
			
			steps {
				//sh 'docker build -t seidut/effulgencetech-nodejs-image:$BUILD_NUMBER .'
				sh 'docker build -t $IMAGE_REPO_NAME:$BUILD_NUMBER .'
				sh 'docker images'
			}
		}
		
//Logging into Dockerhub
		stage('Login to Dockerhub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

//Building and tagging our Docker container. do not forget to change the port number in the docker run command if you are using different port number in your app.js file.
stage('Build-Container') {
    steps {
        script {
            def port = sh(
                script: '''
                used_ports=$(ss -Htan | awk '{print $4}' | cut -d: -f2 | sort -n | uniq)
                free_port=$(comm -23 <(seq 8000 9000 | sort -n) <(echo "$used_ports") | shuf | head -n 1)
                echo $free_port
                ''',
                returnStdout: true
            ).trim()
           
            echo "Selected free port: ${port}"
 
            // Save port to environment variable if needed
            env.DYNAMIC_PORT = port
 
            // Run your Docker container with dynamic port
            sh """
            docker run --name effulgencetech-nodejs-cont-${BUILD_NUMBER} -p $DYNAMIC_PORT:8080 -d topg528/effulgencetech-nodejs-img:13
            """
        }
    }
}
//Pushing the image to the docker

		stage('Push to Dockerhub') {
			//Pushing image to dockerhub
			steps {
				//sh 'docker push seidut/effulgencetech-nodejs-image:$BUILD_NUMBER'
				sh 'docker push $IMAGE_REPO_NAME:$BUILD_NUMBER'
			}
		}
        
	}

  //  post { 
       // always { 
         //   echo 'I will always say Hello again!'
      //      slackSend channel: '#developers', color: COLOR_MAP[currentBuild.currentResult], message: "*${currentBuild.currentResult}:*, Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
    //    }
  //  }

}
