pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {

        stage('Code-Compile') {
            steps {
               sh "mvn clean compile"
            }
        }
        
        stage('Unit Tests') {
            steps {
               sh "mvn test"
            }
        }
        
		stage('OWASP Dependency Check') {
            steps {
               dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }


        stage('Sonar Analysis') {
            steps {
               withSonarQubeEnv('sonar'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Santa \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Santa1 '''
               }
            }
        }

		 
        stage('Code-Build') {
            steps {
               sh "mvn clean package"
            }
        }

         stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker build -t  santa123 . "
                 }
               }
            }
        }

        stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker tag santa123 vamsi01/santa123:latest"
                    sh "docker push vamsi01/santa123:latest"
                 }
               }
            }
        }
        
        
        
		
	stage ('Docker Image Scan') {
     steps {
        sh "trivy image vamsi01/santa123:latest "
     }
} }

 post {
      always {
              emailext   (
                subject:  "Pipeline Status: ${BUILD_NUMBER}" ,
	     body: """<html>
	                        <body>                     
	                              <p> Build status: ${BUILD_STATUS}</p>
	                              <p> Build Number: ${BUILD_NUMBER}</p>
	                              <p> check the <a href=" ${BUILD_URL}" > console output  </a>.</p>
	                       </body>  
	                 </html>""",
	  to:  'vamsikrris01@gmail.com',
	  from: 'jenkins@example.com',
	 replyTo: 'jenkins@example.com',
	mimeType: 'text/html'
     )
  }
}
	

    
}
