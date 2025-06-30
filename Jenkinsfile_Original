pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
         LOG_FILE = 'console_output.txt'
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

       
      
        stage('Full Build Logging') {
            steps {
                // 👇 Use Bash explicitly so redirection works
                sh '''
                    #!/bin/bash

                    # Remove previous log if any
                    rm -f $LOG_FILE

                    # Redirect all stdout and stderr to console and file
                    exec > >(tee -a $LOG_FILE)
                    exec 2>&1

                    echo "==== Build Started ===="
                    echo "Running some dummy work..."
                    ls -l /tmp
                    sleep 1
                    echo "==== Build Completed ===="
                '''
            }
        }
    }

    post {
        always {
            emailext(
                subject: "Pipeline Log: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<html><body>
                    <p>Status: ${currentBuild.currentResult}</p>
                    <p>Check full log attached or view it <a href="${env.BUILD_URL}">here</a>.</p>
                </body></html>""",
                to: 'vamshirockz42@gmail.com',
                attachmentsPattern: "${env.LOG_FILE}",
                mimeType: 'text/html'
            )
        }
    }
}
