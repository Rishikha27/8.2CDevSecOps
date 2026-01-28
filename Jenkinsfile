pipeline {
    agent any

    tools {
        nodejs "nodejs" // Make sure this matches the NodeJS tool in Jenkins
    }

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

        stage('NPM Audit (Security Scan)') {
            steps {
                echo "Running npm audit to check for known vulnerabilities"
                sh 'npm audit || true' // Shows vulnerabilities without failing the pipeline
            }
        }

        stage('Archive Audit Report') {
            steps {
                echo "Saving npm audit report"
                sh 'npm audit --json > npm-audit-report.json || true'
                archiveArtifacts artifacts: 'npm-audit-report.json', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! Check the archived audit report."
        }
        failure {
            echo "Pipeline failed. Check previous stages for errors."
        }
    }
}
