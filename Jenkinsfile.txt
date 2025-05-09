pipeline {
    agent any

    environment {
        // SonarQube server URL
        SONAR_HOST_URL = 'http://localhost:9000'  // Adjust if your SonarQube server URL is different
        // SonarQube authentication token (use the token you provided)
        SONAR_AUTH_TOKEN = 'sqa_4d7f9fc65b0829926f8f4176607a73c4ac467292'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube scanner to analyze the code
                    withSonarQubeEnv('SonarQube') {
                        sh ''' 
                            sonar-scanner \
                            -Dsonar.projectKey=sonar-python-demo \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_AUTH_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for the analysis to finish and check the quality gate status
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            // Clean up any resources (optional)
            echo 'Pipeline finished!'
        }
    }
}
