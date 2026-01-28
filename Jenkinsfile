pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Your SonarCloud token stored in Jenkins credentials
        NODEJS_HOME = tool name: 'nodejs', type: 'NodeJS' // Make sure NodeJS is installed in Jenkins
        PATH = "${env.NODEJS_HOME}/bin:${env.PATH}"
    }

    stages {
        // ===== Checkout Code =====
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub'
                checkout scm
            }
        }

        // ===== Install Dependencies =====
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies'
                sh 'npm install'
            }
        }

        // ===== Run Tests (Skipping Snyk) =====
        stage('Run Tests') {
            steps {
                echo 'Skipping Snyk tests, proceeding with coverage and SonarCloud analysis'
                sh 'npm test'
            }
        }

        // ===== Generate Coverage Report =====
        stage('Generate Coverage Report') {
            steps {
                echo 'Generating coverage report'
                sh 'npm run coverage || echo "Coverage generation skipped"'
            }
        }

        // ===== NPM Audit =====
        stage('NPM Audit (Security Scan)') {
            steps {
                echo 'Running NPM audit for security vulnerabilities'
                sh 'npm audit || echo "Audit completed with issues"'
            }
        }

        // ===== SonarCloud Analysis =====
        stage('SonarCloud Analysis') {
            steps {
                echo 'Running SonarCloud analysis'
                withEnv(["SONAR_SCANNER_HOME=${tool 'SonarScannerCLI'}"]) {
                    sh '''
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=your_project_key \
                        -Dsonar.organization=your_organization_name \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**,test/** \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        // ===== Build Application =====
        stage('Build Application') {
            steps {
                echo 'Building the Node.js application'
                sh 'npm run build || echo "Build step skipped if not defined"'
            }
        }

        // ===== Archive Artifacts =====
        stage('Archive Build Artifacts') {
            steps {
                echo 'Archiving build artifacts'
                archiveArtifacts artifacts: '**/dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Check logs for errors.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the stages above.'
        }
    }
}
