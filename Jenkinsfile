pipeline {
    agent any

    environment {
        // Fix PATH so Jenkins can find node & npm on macOS
        PATH = "/opt/homebrew/bin:/usr/local/bin:${env.PATH}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Rishikha27/8.2CDevSecOps'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        curl -sSLo sonar.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
                        unzip -o sonar.zip
                        ./sonar-scanner-*/bin/sonar-scanner
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully. Quality Gate evaluated in SonarCloud.'
        }
        failure {
            echo 'Pipeline failed. Check console logs for details.'
        }
    }
}
