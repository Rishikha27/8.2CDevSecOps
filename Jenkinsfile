pipeline {
    agent any

    tools {
        nodejs "nodejs" // NodeJS tool name in Jenkins
    }

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Securely access SonarCloud token
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

        stage('Run Tests') {
            steps {
                echo "Running npm tests (pipeline continues even if tests fail)"
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                echo "Generating coverage report (if available)"
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                echo "Running npm audit for known vulnerabilities"
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                echo "Running SonarCloud analysis"
                sh '''
                #!/bin/bash
                # Download SonarScanner CLI
                curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.12.0.3872-macosx.zip
                unzip -qq sonar-scanner.zip
                export PATH=$PWD/sonar-scanner-5.12.0.3872-macosx/bin:$PATH
                sonar-scanner -Dsonar.login=$SONAR_TOKEN
                '''
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
            echo "Pipeline executed successfully! Check SonarCloud and audit report."
        }
        failure {
            echo "Pipeline failed. Check previous stages for errors."
        }
    }
}
