pipeline {
    agent any

    // Use NodeJS tool configured in Jenkins Global Tool Configuration
    tools {
        nodejs "nodejs"  // Make sure this matches the name in Jenkins tools
    }

    environment {
        // Save npm audit results to a JSON file for reporting
        AUDIT_REPORT = "npm-audit-report.json"
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
                echo "Generating code coverage report"
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                echo "Running npm audit to check for known vulnerabilities"
                // Save audit results to a file
                sh "npm audit --json > ${AUDIT_REPORT} || true"
            }
        }

        stage('Review Vulnerabilities') {
            steps {
                script {
                    def audit = readJSON file: env.AUDIT_REPORT
                    def totalVuln = audit.metadata.vulnerabilities.critical +
                                    audit.metadata.vulnerabilities.high +
                                    audit.metadata.vulnerabilities.moderate +
                                    audit.metadata.vulnerabilities.low
                    echo "Total vulnerabilities found: ${totalVuln}"

                    // Optional: fail pipeline if critical/high found
                    if (audit.metadata.vulnerabilities.critical > 0 || audit.metadata.vulnerabilities.high > 0) {
                        echo "Critical or high vulnerabilities found!"
                    }
                }
            }
        }

        stage('Archive Audit Report') {
            steps {
                echo "Archiving npm audit report"
                archiveArtifacts artifacts: "${AUDIT_REPORT}", fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! Check console output and audit report."
        }
        failure {
            echo "Pipeline failed. Check stages and console output for details."
        }
    }
}
