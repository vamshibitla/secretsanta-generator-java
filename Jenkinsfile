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

        stage('Example') {
            steps {
                echo "Starting example stage..."
                echo "Running some dummy commands"
                echo "Finishing up"
            }
        }
    stage('Build') {
            steps {
                // Capture output to console_output.txt AND show on console
                sh '''
                    echo "Starting build process..." | tee console_output.txt
                    echo "Running commands..." | tee -a console_output.txt
                    echo "Finishing build..." | tee -a console_output.txt
                '''
            }
        }
    }

    post {
        always {
            emailext(
                subject: "Pipeline Status: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<html>
                    <body>
                        <p><b>Status:</b> ${currentBuild.currentResult}</p>
                        <p><a href="${env.BUILD_URL}">Click to view full console output</a></p>
                    </body>
                </html>""",
                to: 'vamsikrris01@gmail.com',
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html',
                attachmentsPattern: 'console_output.txt'
            )
        }
    }
}

      


