pipeline {
    agent any

    environment {
        // SonarQube server URL
        SONAR_HOST_URL = 'http://localhost:9000'
        // Updated SonarQube authentication token
        SONAR_AUTH_TOKEN = 'sqa_1ebae7b0ace5ef257098ede22a1db4a0068c6bad'
        // GitHub authentication token (using the credential ID 'GithubToken')
        GITHUB_TOKEN = credentials('GithubToken') // Reference to the GitHub personal access token stored in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code using GitHub token
                    withCredentials([string(credentialsId: 'GithubToken', variable: 'GITHUB_TOKEN')]) {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/main']], // Or use the branch you want
                            doGenerateSubmoduleConfigurations: false,
                            extensions: [],
                            userRemoteConfigs: [[
                                url: 'https://github.com/shivasaiteja123/sonar-python-demo.git',
                                credentialsId: 'GithubToken' // Referencing the GitHub token from Jenkins credentials
                            ]]
                        ])
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run the SonarQube scan using the configured SonarQube environment and authentication token
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
                    // Wait for the quality gate result and abort if it fails
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished!'
        }
    }
}
