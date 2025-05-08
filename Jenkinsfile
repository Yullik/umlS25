pipeline {
    agent { label 'docker-agent' }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Main Branch Tests') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                echo 'Running tests and code coverage for main branch'
                sh './gradlew test'  // Run tests
                sh './gradlew jacocoTestReport'  // Generate Jacoco report for code coverage
            }
        }

        stage('Feature Branch Tests') {
            when {
                branch 'feature/*'
            }
            steps {
                echo 'Running unit tests on feature branch'
                sh './gradlew test'  // Run unit tests on feature branch
            }
        }

        stage('Other Branch Failure') {
            when {
                not {
                    anyOf {
                        branch 'main'
                        branch 'feature/*'
                    }
                }
            }
            steps {
                echo 'This is an unknown branch (other-branch), pipeline failed.'
            }
        }
    }

    post {
        success {
            echo "Pipeline ran successfully"
            archiveArtifacts '**/build/reports/jacoco/test/jacocoTestReport.xml'  // Archive Jacoco report
            archiveArtifacts '**/build/reports/jacoco/test/html/*.html'  // Archive Jacoco HTML report
        }
        failure {
            echo "Pipeline failed"
            script {
                currentBuild.result = 'FAILURE'  // Set build result to FAILURE if pipeline fails
            }
        }
    }
}

