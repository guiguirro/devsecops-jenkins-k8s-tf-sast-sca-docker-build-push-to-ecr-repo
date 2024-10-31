pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=dvldvldvldvldvldvldvl -Dsonar.organization=dvldvldvldvldvldvldvl -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=f90b09f8c58d0f0c898e35b34ffe6de0c1c0a9c8'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

stage('Build') {
    steps {
        withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
            sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 010438463494.dkr.ecr.us-east-1.amazonaws.com"
                
            """
        }

        withDockerRegistry([credentialsId: "aws-ecr-credentials", url: "https://010438463494.dkr.ecr.us-east-1.amazonaws.com"]) {
            script{
                app =  docker.build("asg")
            }
        }
    }
}

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://010438463494.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
