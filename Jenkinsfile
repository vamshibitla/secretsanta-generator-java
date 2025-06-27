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
    }

    post {
        always {
            script {
                // Capture full console log (no line limit)
                def fullLog = currentBuild.rawBuild.getLog().join("\n")
                
                // Write to a file in workspace
                writeFile file: 'console_output.txt', text: fullLog
            }

            // Send email with log file attached
            emailext(
                subject: "Pipeline Status: ${env.BUILD_NUMBER}",
                body: """<html>
                    <body>
                        <p><b>Build status:</b> ${currentBuild.currentResult}</p>
                        <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                        <p><a href="${env.BUILD_URL}">Click here for full build console</a></p>
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
        


