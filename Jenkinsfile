pipeline {
    agent any

    // Tools used in the pipeline
    tools {
        maven 'Maven-3.8.4'    // Must match Manage Jenkins → Global Tool Configuration
        jdk 'OpenJDK-11'
    }

    // Environment variables available in all stages
    environment {
        RUN_TESTS = 'true'
        APP_ENV = 'staging'
        NODE_VERSION = '16'
    }

    // Parameters to control pipeline externally
    parameters {
        booleanParam(name: 'EXECUTE_TESTS', defaultValue: true, description: 'Run tests?')
        string(name: 'DEPLOY_ENV', defaultValue: 'staging', description: 'Deployment environment')
    }

    stages {
        stage('Prepare') {
            steps {
                echo "Preparing agent..."
                sh 'java -version || true'
            }
        }

        stage('Build') {
            steps {
                echo "Building project..."
                sh 'mvn -B -DskipTests package'  // adjust to your project
            }
        }

        stage('Test') {
            // Conditional: only run if EXECUTE_TESTS is true and RUN_TESTS env var is true
            when {
                allOf {
                    expression { params.EXECUTE_TESTS == true }
                    expression { env.RUN_TESTS == 'true' }
                }
            }
            steps {
                echo "Running tests..."
                sh 'mvn test'  // or your own test command
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying to ${params.DEPLOY_ENV} environment..."
                // sh './scripts/deploy.sh ${params.DEPLOY_ENV}'  // optional deploy script
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Build completed successfully!'
        }
        failure {
            echo 'FAILURE: Build failed!'
        }
        always {
            echo 'ALWAYS: This runs at the end, e.g., cleanup tasks.'
        }
    }
}
