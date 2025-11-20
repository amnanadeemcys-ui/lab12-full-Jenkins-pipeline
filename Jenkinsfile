/*
 * Jenkins Declarative Pipeline for Continuous Integration and Continuous Delivery (CI/CD)
 * FIX: The Tool ID is changed to 'SonarScannerFinal' to force a clean re-installation 
 * by Jenkins, resolving the "The system cannot find the path specified" error.
 */
pipeline {
    agent any

    // FIX: Changed tool ID to force reinstallation.
    tools {
        'hudson.plugins.sonar.SonarRunnerInstallation' 'SonarScannerFinal' 
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
                
                // 1. Manually resolve the tool path using the new ID.
                def sonarScannerHome = tool 'SonarScannerFinal'
                def scannerCommand = "${sonarScannerHome}\\bin\\sonar-scanner.bat"
                
                // 2. Use the path inside the withSonarQubeEnv block
                withSonarQubeEnv('lab12') { 
                    
                    // The 'bat' command with triple-double quotes for robust execution on Windows.
                    bat """"${scannerCommand}" -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=.""" 
                }
                
                // Quality Gate Check: Pause Pipeline until SonarQube completes analysis
                script {
                    echo 'Waiting for SonarQube Quality Gate result...'
                    timeout(time: 5, unit: 'MINUTES') { 
                        def qg = waitForQualityGate() 
                        
                        if (qg.status != 'OK') {
                            error "SAST Failed! Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                    echo 'Quality Gate passed. Proceeding to next stage.'
                }
            }
        }

        stage('Test') {
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
        }
    }
}
