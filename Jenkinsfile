/*
 * Jenkins Declarative Pipeline for Continuous Integration and Continuous Delivery (CI/CD)
 * This pipeline includes a Static Application Security Testing (SAST) stage using SonarQube
 * and enforces a Quality Gate check.
 */
pipeline {
    agent any // Specifies that the entire pipeline can run on any available agent

    // Global tools setup: Jenkins will automatically download and install the scanner
    // FIX APPLIED: Using the fully qualified class name for the SonarQube Scanner tool
    // This name is guaranteed to be recognized by your Jenkins instance.
    tools {
        'hudson.plugins.sonar.SonarRunnerInstallation' 'SonarScannerCLI' 
    }
    
    stages {
        // 1. Build Stage
        stage('Build') {
            steps {
                echo 'Building the project...'
                // You can add your actual build/compile command here
            }
        }
        
        // 2. Static Analysis Stage (SAST)
        stage('SonarQube Analysis (SAST)') {
            steps {
                echo 'Starting Static Analysis with SonarQube...'
                
                // withSonarQubeEnv prepares the environment variables (like the token) 
                // for the specified server.
                // 'MySonarQubeServer' must match the name configured in Jenkins
                withSonarQubeEnv('MySonarQubeServer') { 
                    // 'bat' command used for Windows. This executes the scanner.
                    // IMPORTANT: -Dsonar.projectKey=my-devsecops-project MUST match the key in SonarQube UI.
                    bat "sonar-scanner -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=." 
                }
                
                // Quality Gate Check: Pause Pipeline until SonarQube completes analysis
                script {
                    echo 'Waiting for SonarQube Quality Gate result...'
                    timeout(time: 5, unit: 'MINUTES') { 
                        def qg = waitForQualityGate() 
                        // If the status is not 'OK' (meaning it failed the security/quality checks), 
                        // the entire Jenkins build fails.
                        if (qg.status != 'OK') {
                            error "SAST Failed! Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                    echo 'Quality Gate passed. Proceeding to next stage.'
                }
            }
        }

        // 3. Test Stage (Only runs if Quality Gate is OK)
        stage('Test') {
            steps {
                echo 'Testing the project...'
                // Add your testing commands here
            }
        }
        
        // 4. Deploy Stage
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                // Add your deployment commands here
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
