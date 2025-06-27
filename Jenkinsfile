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

        
    }

    post {
        always {
            script {
                // ✅ Get full log safely (without getRawBuild)
                def fullLog = currentBuild.getLog().join("\n")
                writeFile file: "console_output.txt", text: fullLog
            }

            emailext(
                subject: "Pipeline Log: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<html>
                    <body>
                        <p><b>Status:</b> ${currentBuild.currentResult}</p>
                        <p><a href="${env.BUILD_URL}">Click here to view in Jenkins</a></p>
                    </body>
                </html>""",
                to: 'vamshirockz42@gmail.com',
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html',
                attachmentsPattern: 'console_output.txt'
            )
        }
    }

}  


