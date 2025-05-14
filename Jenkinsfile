pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_AUTH_TOKEN = 'sqa_1ebae7b0ace5ef257098ede22a1db4a0068c6bad'
        GITHUB_TOKEN = credentials('GithubToken')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'GithubToken', variable: 'GITHUB_TOKEN')]) {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/main']],
                            doGenerateSubmoduleConfigurations: false,
                            extensions: [],
                            userRemoteConfigs: [[
                                url: 'https://github.com/shivasaiteja123/sonar-python-demo.git',
                                credentialsId: 'GithubToken'
                            ]]
                        ])
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        bat """
                            C:\\SonarScanner\\sonar-scanner-7.0.2.4839-windows-x64\\bin\\sonar-scanner ^ 
                            -Dsonar.projectKey=sonar-python-demo ^ 
                            -Dsonar.sources=. ^ 
                            -Dsonar.host.url=%SONAR_HOST_URL% ^ 
                            -Dsonar.login=%SONAR_AUTH_TOKEN%
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished! Sending email notification...'
            emailext (
                subject: "Jenkins Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: """
                    <p><b>Jenkins Pipeline Execution Report</b></p>
                    <p><b>Project:</b> ${env.JOB_NAME}</p>
                    <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> ${currentBuild.currentResult}</p>
                    <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>SonarQube Report:</b> <a href="http://localhost:9000/dashboard?id=sonar-python-demo">View Report</a></p>
                """,
                mimeType: 'text/html',
                to: 'yerramchattyshivasaiteja2003@gmail.com',  // <-- Replace with your manager's email or your email
                replyTo: 'no-reply@jenkins.local',  // <-- Add a custom reply-to if necessary
                from: 'saiteja.y@coresonant.com'  // <-- Customize the "From" address as needed
            )
        }
        success {
            echo 'Build successful. Sending success email notification.'
        }
        failure {
            echo 'Build failed. Sending failure email notification.'
        }
    }
}
