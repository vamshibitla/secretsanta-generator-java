pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        // stage('Git checkout') {
        //     steps {
        //        git 'https://github.com/vamshibitla/secretsanta-generator-java.git'
        //     }
        // }
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Tests') {
            steps {
               sh "mvn test"
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=santa \
                     -Dsonar.projectKey=santa -Dsonar.java.binaries=. '''
                }
            }
        }
        stage('Owasp Scan') {
            steps {
               dependencyCheck additionalArguments: ' --scan . ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Build Application') {
            steps {
               sh "mvn package"
            }
        }
        post {
      always {
              emailext   (
                subject:  "Pipeline Status: ${BUILD_NUMBER} ",
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
    
}
