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
                // Read the full console log from current build
                def logText = readFile("${env.WORKSPACE}/../${env.JOB_NAME}/builds/${env.BUILD_ID}/log")

                // Save it as a .txt file in workspace
                writeFile file: "console_output.txt", text: logText
            }

            emailext(
                subject: "Pipeline Log: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<html>
                    <body>
                        <p><b>Status:</b> ${currentBuild.currentResult}</p>
                        <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                        <p><a href="${env.BUILD_URL}">View in Jenkins</a></p>
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
        


