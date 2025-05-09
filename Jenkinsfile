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
                script {
                    if (fileExists('gradlew')) {
                        sh 'chmod +x gradlew'
                    }
                    sh './gradlew test'
                    sh './gradlew jacocoTestReport'
                }
            }
        }

        stage('Feature Branch Tests') {
            when {
                branch 'feature/*'
            }
            steps {
                script {
                    if (fileExists('gradlew')) {
                        sh 'chmod +x gradlew'
                        sh './gradlew test'
                    } else {
                        echo 'Gradle wrapper (gradlew) not found. Running tests with the system Gradle.'
                        sh 'gradle test'
                    }
                }
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


