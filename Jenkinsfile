pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code from GitHub"
                git branch: 'main', url: 'https://github.com/Rishikha27/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing Node.js dependencies"
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running npm tests (pipeline continues even if tests fail)"
                sh 'npm test || true' // allows pipeline to continue
            }
        }

        stage('Generate Coverage Report') {
            steps {
                echo "Generating code coverage report"
                sh 'npm run coverage || true' // continues even if coverage script fails
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                echo "Running npm audit to check for known vulnerabilities"
                sh 'npm audit || true' // shows known CVEs without failing pipeline
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! Check console output for audit results."
        }
        failure {
            echo "Pipeline failed. Check previous stages for errors."
        }
    }
}
