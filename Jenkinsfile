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
                sh 'gradle test'
                sh 'gradle jacocoTestReport'
            }
        }

        stage('Feature Branch Tests') {
            when {
                branch 'feature/*'
            }
            steps {
                echo 'Running unit tests on feature branch'
                sh 'gradle test'
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
            archiveArtifacts '**/build/reports/jacoco/test/jacocoTestReport.xml'
            archiveArtifacts '**/build/reports/jacoco/test/html/*.html'
        }
        failure {
            echo "Pipeline failed"
            script {
                currentBuild.result = 'FAILURE'
            }
        }
    }
}

