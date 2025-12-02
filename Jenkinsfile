
pipeline {
    //Amna Nadeem (22I-1593) - Verified pipeline configuration
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
=======
/*
 * Jenkins Declarative Pipeline for Continuous Integration and Continuous Delivery (CI/CD)
 * This is the corrected version that addresses:
 * 1. Groovy Syntax Error (def/tool must be inside script {}).
 * 2. Tool Name Mismatch (reverting to 'SonarScannerCLI' as suggested by Jenkins).
 * 3. Windows Path Execution Error (using robust triple-double quotes and explicit path).
 */
pipeline {
    agent any

    // 1. Tool name reverted to 'SonarScannerCLI' as suggested by Jenkins.
    tools {
        'hudson.plugins.sonar.SonarRunnerInstallation' 'SonarScannerCLI' 
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }
        
        // 2. Static Analysis Stage (SAST)
        stage('SonarQube Analysis (SAST)') {
            steps {
                echo 'Starting Static Analysis with SonarQube...'
                
                // FIX 1: Variables and the 'tool' step MUST be inside a script block 
                // when used within the 'steps' section.
                script {
                    // 1. Resolve the tool path and store it in a Groovy variable.
                    def sonarScannerHome = tool 'SonarScannerCLI'
                    def scannerCommand = "${sonarScannerHome}\\bin\\sonar-scanner.bat"
                    
                    // 2. Use the path inside the withSonarQubeEnv block
                    withSonarQubeEnv('lab12') { 
                        // The 'bat' command uses triple-double quotes ("""...""") for robust 
                        // execution on Windows, solving the previous path issue.
                        bat """"${scannerCommand}" -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=.""" 
                    }
                }
                
                // Quality Gate Check: Pause Pipeline until SonarQube completes analysis
                script {
                    echo 'Waiting for SonarQube Quality Gate result...'
                    timeout(time: 5, unit: 'MINUTES') { 
                        // This uses the results submitted in the previous 'withSonarQubeEnv' block.
                        def qg = waitForQualityGate() 
                        
                        if (qg.status != 'OK') {
                            error "SAST Failed! Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                    echo 'Quality Gate passed. Proceeding to next stage.'
                }
>>>>>>> ee8747ea0769a2d5cc7e54524bfa9381531caec0
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
=======
            steps {
                echo 'Testing the project...'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished.'
>>>>>>> ee8747ea0769a2d5cc7e54524bfa9381531caec0
        }
    }
}
