pipeline {

    agent { label 'docker-agent' }

    stages {
        // Hello stage for the main branch
        stage("Hello") {
            when {
                expression { env.BRANCH_NAME == 'main' } 
            }
            steps {
                echo 'Hello World'
            }
        }

        // Feature branch check
        stage('Wildcard Branch Check') {
            when {
                branch 'feature/*' 
            }
            steps {
                echo "Running on a feature branch."
            }
        }

        // Default stage for other branches
        stage('Unknown Branch') {
            when {
                not {
                    anyOf {
                        branch 'main'
                        branch 'feature/*'
                    }
                }
            }
            steps {
                echo "Unknown branch, pipeline failed."
                currentBuild.result = 'FAILURE'  // Marking the pipeline as failed
            }
        }
    }

    post {
        success {
            echo "Pipeline ran successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
