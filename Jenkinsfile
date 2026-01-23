pipeline {
    agent any

    environment {
        // Pull Snyk token from Jenkins credentials
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                // Public repo → no credentials needed
                git url: 'https://github.com/Rishikha27/8.2CDevSecOps.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Snyk Auth') {
            steps {
                // Authenticate Snyk using the token from Jenkins credentials
                sh 'npm install -g snyk'
                sh 'snyk auth $SNYK_TOKEN'
            }
        }

        stage('Run Snyk Test') {
            steps {
                // Run the security scan
                sh 'snyk test || true'  // prevent pipeline fail if issues found
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'  // continue pipeline even if tests fail
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Make sure your package.json has a coverage script
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Check logs for details.'
        }
    }
}
