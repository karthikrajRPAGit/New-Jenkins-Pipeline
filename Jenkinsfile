pipeline {
	agent any
	environment
		{
			dockerHome = tool 'myDocker'
			mavenHome= tool 'myMaven'
			PATH="$dockerHome/bin:$mavenHome/bin:$PATH"

		}
	stages {
		stage('CheckOut')
		{
			steps
			{
				sh 'mvn --version'
				echo "BuildID: $env.BUILD_ID"
				echo "BuildTag: $env.BUILD_TAG"
				echo "BuildURL: $env.BUILD_URL"
				echo "JobName: $env.JOB_NAME"
			}
			
		}
		
		stage('compile')
		{
			steps
			{
				sh "mvn clean compile"
				sh "mvn clean package"
			}
			//ssh "mvn clean compile"
		}

		stage('Test')
		{
			steps
			{
				sh "mvn test"
			}
		}

		stage('Integration Test')
		{
			steps
			{
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}

		stage('Docker Build')
		{
			steps
			{
				script
				{
					//docker.withregistry('','dockerConn')
					//{
					//docker build -t dockthik/currency-exchange-jenkins:${env.BUILD_TAG}
					dockerImage=docker.build("dockthik/currency-exchange-jenkins:${env.BUILD_TAG}")
					//}
				}
				
			}
		}

		stage('Docker Push')
		{
			steps
			{
				script
				{
					docker.withRegistry('','dockerConn')
					{
					//docker build -t dockthik/currency-exchange-jenkins:${env.BUILD_TAG}
					//docker.build("dockthik/currency-exchange-jenkins:${env.BUILD_TAG}")
						dockerImage.push()
						dockerImage.push('latest')
					}

				}
				
			}
		}
		
	}

	post
	{
		success 
		{
			echo "I run when I am successful"
		}

		failure
		{
			echo "I run when the build fails"
		}
	}
}
