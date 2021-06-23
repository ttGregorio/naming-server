//SCRIPTED

//DECLARATIVE
pipeline {
	agent any
	// agent { docker { image 'maven:3.6.3'} }
	// agent { docker { image 'node:13.8'} }
	environment {
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
		registry= "527222548725.dkr.ecr.us-east-2.amazonaws.com/eureka-server:$env.BUILD_TAG"
	}

	stages {
		stage('Checkout') {
			steps {
				sh 'mvn --version'
				sh 'docker version'
				echo "Build"
				echo "PATH - $PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_ID - $env.BUILD_ID"
				echo "JOB_NAME - $env.JOB_NAME"
				echo "BUILD_TAG - $env.BUILD_TAG"
				echo "BUILD_URL - $env.BUILD_URL"
			}
		}
		stage('Compile') {
			steps {
				sh "mvn clean compile"
			}
		}

		stage('Test') {
			steps {
				sh "mvn test"
			}
		}

		stage('Integration Test') {
			steps {
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}

		stage('Package') {
			steps {
				sh "mvn package -DskipTests"
			}
		}

		stage('Build Docker Image') {
			steps {
				//"docker build -t eureka-server:$env.BUILD_TAG"
				script {
					dockerImage = docker.build registry
				}

			}
		}

		stage('Push Docker Image') {
			steps {
				script {
//					docker.withRegistry('', 'dockerhub') {
//						dockerImage.push();
//						dockerImage.push('latest');
//					}

					docker.withRegistry('https://527222548725.dkr.ecr.us-east-2.amazonaws.com', 'ecr:us-east-2:aws_credentials') {
						dockerImage.push();
						dockerImage.push('latest');
					}
				}
			}
		}
	} 
	
	post {
		success {
			echo 'Success'
		}
		failure {
			echo 'Error'
		}
	}
}