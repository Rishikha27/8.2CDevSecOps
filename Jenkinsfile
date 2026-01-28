pipeline {
    agent any

    environment {
        // Use the SONAR_TOKEN stored in Jenkins credentials
        SONAR_TOKEN = credentials('SONAR_TOKEN')
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
                sh 'npm audit --json || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                echo "Running SonarCloud analysis"
                sh '''
                    #!/bin/bash
                    # Download SonarScanner CLI (if not installed)
                    if [ ! -d sonar-scanner-cli ]; then
                        curl -o sonar-scanner-cli.zip -L https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.9.3.7499-macosx.zip
                        unzip sonar-scanner-cli.zip
                    fi
                    # Run SonarScanner
                    ./sonar-scanner-4.9.3.7499-macosx/bin/sonar-scanner \
                      -Dsonar.projectKey=<your_project_key> \
                      -Dsonar.organization=<your_organization_name> \
                      -Dsonar.host.url=https://sonarcloud.io \
                      -Dsonar.login=$SONAR_TOKEN \
                      -Dsonar.sources=. \
                      -Dsonar.exclusions=node_modules/**,test/** \
                      -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                      -Dsonar.projectName="NodeJS Goof Vulnerable App" \
                      -Dsonar.sourceEncoding=UTF-8
                '''
            }
        }

        stage('Build Application') {
            steps {
                echo "Building Node.js application"
                sh '''
                    # Generic build step
                    mkdir -p dist
                    cp -r src/* dist/ || true
                    echo "Build completed. Files copied to dist/"
                '''
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                echo "Archiving build artifacts"
                archiveArtifacts artifacts: 'dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! Check console output for details."
        }
        failure {
            echo "Pipeline failed. Check previous stages for errors."
        }
    }
}
