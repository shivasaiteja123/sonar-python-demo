pipeline {
    agent any

    environment {
        SONARQUBE_ENV = credentials('SonarqubeToken') // SonarQube token
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_PROJECT_KEY = 'sonar-python-demo'
        POSTMARK_API_KEY = credentials('postmark-api-key')
        POSTMARK_FROM = 'saiteja.y@coresonant.com'
        POSTMARK_TO = 'yerramchattyshivasaiteja2003@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat """
                        C:\\SonarScanner\\sonar-scanner-7.0.2.4839-windows-x64\\bin\\sonar-scanner ^
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} ^
                        -Dsonar.sources=. ^
                        -Dsonar.host.url=${SONAR_HOST_URL} ^
                        -Dsonar.token=${SONARQUBE_ENV}
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qualityGate = waitForQualityGate()
                        currentBuild.result = qualityGate.status == 'OK' ? 'SUCCESS' : 'FAILURE'

                        // Send Email After Quality Gate
                        def subject = "SonarQube Quality Gate: ${qualityGate.status}"
                        def textBody = "SonarQube Quality Gate Status: ${qualityGate.status}\n" +
                                       "Check results: ${SONAR_HOST_URL}/dashboard?id=${SONAR_PROJECT_KEY}"

                        def response = httpRequest(
                            httpMode: 'POST',
                            url: 'https://api.postmarkapp.com/email',
                            customHeaders: [
                                [name: 'X-Postmark-Server-Token', value: "${env.POSTMARK_API_KEY}"],
                                [name: 'Content-Type', value: 'application/json']
                            ],
                            requestBody: """
                            {
                                "From": "${env.POSTMARK_FROM}",
                                "To": "${env.POSTMARK_TO}",
                                "Subject": "${subject}",
                                "TextBody": "${textBody}"
                            }
                            """.stripIndent(),
                            validResponseCodes: '200'
                        )
                        echo "Postmark response: ${response}"
                    }
                }
            }
        }
    }
}
