pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('Compile and Run Sonar Analysis') {
            steps {	
        sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=dvldvldvldvldvldvldvl -Dsonar.organization=dvldvldvldvldvldvldvl -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=f90b09f8c58d0f0c898e35b34ffe6de0c1c0a9c8'
            }
    }

    stage('Run SCA Analysis Using Snyk') {
            steps {		
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'mvn snyk:test -fn'
                }
            }
    }

    stage('Build') {
            steps {
                withAWS([credentials: 'aws-credentials', region: 'us-east-1']) {
                    sh 'aws ecr get-login-password --region us-east-1'
                }
                withDockerRegistry([credentialsId: 'aws-ecr-credentials', url: 'https://010438463494.dkr.ecr.us-east-1.amazonaws.com']) {
               app =  docker.build("asg")
                }
            }
        }
    }

    stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://010438463494.dkr.ecr.us-east-1.amazonaws.com', 'aws-ecr-credentials') {
                        app.push("latest")
                    }
                }
            }
    }
  }
