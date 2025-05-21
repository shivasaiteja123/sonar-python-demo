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
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished! Sending email notification via Postmark...'

            script {
                def subject = "Jenkins Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}"
                def body = """
                    <p><b>Jenkins Pipeline Execution Report</b></p>
                    <p><b>Project:</b> ${env.JOB_NAME}</p>
                    <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> ${currentBuild.currentResult}</p>
                    <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>SonarQube Report:</b> <a href="http://localhost:9000/dashboard?id=sonar-python-demo">View Report</a></p>
                """

                def toEmail = 'yerramchattyshivasaiteja2003@gmail.com'
                def fromEmail = 'saiteja.y@coresonant.com'

                withCredentials([string(credentialsId: 'PostmarkApiKey', variable: 'POSTMARK_API_KEY')]) {
                    def payloadMap = [
                        From    : fromEmail,
                        To      : toEmail,
                        Subject : subject,
                        HtmlBody: body
                    ]

                    def jsonPayload = groovy.json.JsonOutput.toJson(payloadMap)
                    echo "JSON Payload: ${jsonPayload}" // For debugging

                    if (isUnix()) {
                        def safeJsonPayload = jsonPayload.replace("'", "'\"'\"'")
                        sh """
                            curl -X POST "https://api.postmarkapp.com/email" \\
                            -H "Accept: application/json" \\
                            -H "Content-Type: application/json" \\
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" \\
                            -d '${safeJsonPayload}' || echo "Postmark email failed"
                        """
                    } else {
                        def escapedJsonPayload = jsonPayload.replace('"', '\\"')
                        bat """
                            curl -X POST "https://api.postmarkapp.com/email" ^
                            -H "Accept: application/json" ^
                            -H "Content-Type: application/json" ^
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" ^
                            -d "${escapedJsonPayload}" || echo Postmark email failed
                        """
                    }
                }
            }
        }

        success {
            echo '✅ Build successful. Email sent.'
        }

        failure {
            echo '❌ Build failed. Email sent.'
        }
    }
}
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
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished! Sending email notification via Postmark...'

            script {
                def subject = "Jenkins Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}"
                def body = """
                    <p><b>Jenkins Pipeline Execution Report</b></p>
                    <p><b>Project:</b> ${env.JOB_NAME}</p>
                    <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> ${currentBuild.currentResult}</p>
                    <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>SonarQube Report:</b> <a href="http://localhost:9000/dashboard?id=sonar-python-demo">View Report</a></p>
                """

                def toEmail = 'yerramchattyshivasaiteja2003@gmail.com'
                def fromEmail = 'saiteja.y@coresonant.com'

                withCredentials([string(credentialsId: 'PostmarkApiKey', variable: 'POSTMARK_API_KEY')]) {
                    def payloadMap = [
                        From    : fromEmail,
                        To      : toEmail,
                        Subject : subject,
                        HtmlBody: body
                    ]

                    def jsonPayload = groovy.json.JsonOutput.toJson(payloadMap)
                    echo "JSON Payload: ${jsonPayload}" // For debugging

                    if (isUnix()) {
                        def safeJsonPayload = jsonPayload.replace("'", "'\"'\"'")
                        sh """
                            curl -X POST "https://api.postmarkapp.com/email" \\
                            -H "Accept: application/json" \\
                            -H "Content-Type: application/json" \\
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" \\
                            -d '${safeJsonPayload}' || echo "Postmark email failed"
                        """
                    } else {
                        def escapedJsonPayload = jsonPayload.replace('"', '\\"')
                        bat """
                            curl -X POST "https://api.postmarkapp.com/email" ^
                            -H "Accept: application/json" ^
                            -H "Content-Type: application/json" ^
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" ^
                            -d "${escapedJsonPayload}" || echo Postmark email failed
                        """
                    }
                }
            }
        }

        success {
            echo '✅ Build successful. Email sent.'
        }

        failure {
            echo '❌ Build failed. Email sent.'
        }
    }
}
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
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished! Sending email notification via Postmark...'

            script {
                def subject = "Jenkins Pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}"
                def body = """
                    <p><b>Jenkins Pipeline Execution Report</b></p>
                    <p><b>Project:</b> ${env.JOB_NAME}</p>
                    <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> ${currentBuild.currentResult}</p>
                    <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>SonarQube Report:</b> <a href="http://localhost:9000/dashboard?id=sonar-python-demo">View Report</a></p>
                """

                def toEmail = 'yerramchattyshivasaiteja2003@gmail.com'
                def fromEmail = 'saiteja.y@coresonant.com'

                withCredentials([string(credentialsId: 'PostmarkApiKey', variable: 'POSTMARK_API_KEY')]) {
                    def payloadMap = [
                        From    : fromEmail,
                        To      : toEmail,
                        Subject : subject,
                        HtmlBody: body
                    ]

                    def jsonPayload = groovy.json.JsonOutput.toJson(payloadMap)
                    echo "JSON Payload: ${jsonPayload}" // For debugging

                    if (isUnix()) {
                        def safeJsonPayload = jsonPayload.replace("'", "'\"'\"'")
                        sh """
                            curl -X POST "https://api.postmarkapp.com/email" \\
                            -H "Accept: application/json" \\
                            -H "Content-Type: application/json" \\
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" \\
                            -d '${safeJsonPayload}' || echo "Postmark email failed"
                        """
                    } else {
                        def escapedJsonPayload = jsonPayload.replace('"', '\\"')
                        bat """
                            curl -X POST "https://api.postmarkapp.com/email" ^
                            -H "Accept: application/json" ^
                            -H "Content-Type: application/json" ^
                            -H "X-Postmark-Server-Token: ${POSTMARK_API_KEY}" ^
                            -d "${escapedJsonPayload}" || echo Postmark email failed
                        """
                    }
                }
            }
        }

        success {
            echo '✅ Build successful. Email sent.'
        }

        failure {
            echo '❌ Build failed. Email sent.'
        }
    }
}
