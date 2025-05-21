pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_AUTH_TOKEN = 'sqa_1ebae7b0ace5ef257098ede22a1db4a0068c6bad'
        // GITHUB_TOKEN will be accessed via withCredentials below
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'GithubToken', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/main']],
                            userRemoteConfigs: [[
                                url: "https://${GIT_USER}:${GIT_PASS}@github.com/shivasaiteja123/sonar-python-demo.git"
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
                            -Dsonar.login=%SONAR_AUTH_TOKEN% ^
                            -Dsonar.python.version=3.10
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
            script {
                echo 'Pipeline finished! Sending email notification via Elastic Email API...'

                withCredentials([string(credentialsId: 'ElasticAPI', variable: 'ELASTIC_API_KEY')]) {
                    def emailSubject = "Jenkins Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}"
                    def emailBody = """
                        <p><b>Jenkins Pipeline Execution Report</b></p>
                        <p><b>Project:</b> ${env.JOB_NAME}</p>
                        <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                        <p><b>Status:</b> ${currentBuild.currentResult}</p>
                        <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                        <p><b>SonarQube Report:</b> <a href="http://localhost:9000/dashboard?id=sonar-python-demo">View Report</a></p>
                    """

                    def jsonPayload = [
                        apikey: ELASTIC_API_KEY,
                        from: "saiteja.y@coresonant.com",      // Must be verified in Elastic Email
                        fromName: "Jenkins CI",
                        subject: emailSubject,
                        to: "yerramchattyshivasaiteja2003@gmail.com",
                        bodyHtml: emailBody,
                        isTransactional: true
                    ]

                    def response = httpRequest(
                        acceptType: 'APPLICATION_JSON',
                        contentType: 'APPLICATION_JSON',
                        httpMode: 'POST',
                        requestBody: groovy.json.JsonOutput.toJson(jsonPayload),
                        url: 'https://api.elasticemail.com/v2/email/send'
                    )

                    echo "Elastic Email API response status: ${response.status}"
                    echo "Elastic Email API response content: ${response.content}"
                }
            }
        }

        success {
            echo 'Build successful. Email sent.'
        }
        failure {
            echo 'Build failed. Email sent.'
        }
    }
}
