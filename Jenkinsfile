/*
 * Jenkins Declarative Pipeline for Continuous Integration and Continuous Delivery (CI/CD)
 * This pipeline includes a Static Application Security Testing (SAST) stage using SonarQube
 * and enforces a Quality Gate check.
 */
pipeline {
    agent any // Specifies that the entire pipeline can run on any available agent

    // Global tools setup: Jenkins will automatically download and install the scanner
    // 'SonarScannerCLI' must match the name configured in Manage Jenkins -> Global Tool Configuration
    tools {
        sonarScanner 'SonarScannerCLI' 
    }
    
    stages {
        // 1. Build Stage
        stage('Build') {
            steps {
                echo 'Building the project...'
                // Add your project's compilation commands here (e.g., mvn clean install, npm run build)
                // For a simple demo, we just echo.
            }
        }
        
        // 2. Static Analysis Stage (SAST)
        stage('SonarQube Analysis (SAST)') {
            steps {
                echo 'Starting Static Analysis with SonarQube...'
                
                // withSonarQubeEnv prepares the environment variables needed for the scanner.
                // 'MySonarQubeServer' must match the name configured in Manage Jenkins -> Configure System
                withSonarQubeEnv('MySonarQubeServer') { 
                    // 'bat' command is used because you are running Jenkins on Windows.
                    // -Dsonar.projectKey=my-devsecops-project MUST match the Project Key you created in SonarQube UI.
                    // -Dsonar.sources=. tells the scanner to analyze code in the current directory.
                    bat "sonar-scanner -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=." 
                }
                
                // Quality Gate Check: Pause Pipeline until SonarQube completes analysis
                script {
                    echo 'Waiting for SonarQube Quality Gate result...'
                    timeout(time: 5, unit: 'MINUTES') { 
                        // The function retrieves the Quality Gate status (OK or FAILED)
                        def qg = waitForQualityGate() 
                        // If status is not OK, the pipeline immediately stops and fails.
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
                // Add your testing commands here (e.g., mvn test, npm test)
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
    
    // Optional: Add a post-build action (like email notification, cleanup, etc.)
    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
