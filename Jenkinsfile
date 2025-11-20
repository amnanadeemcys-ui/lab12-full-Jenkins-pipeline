/*
 * Jenkins Declarative Pipeline for Continuous Integration and Continuous Delivery (CI/CD)
 * FIX: This version explicitly resolves the installation path for 'sonar-scanner.bat'
 * to fix the "'sonar-scanner' is not recognized" error.
 */
pipeline {
    agent any

    // Global tools setup: Jenkins will automatically download and install the scanner
    // Tool Name: 'hudson.plugins.sonar.SonarRunnerInstallation' is the explicit, working class name.
    // Tool ID: 'SonarScannerCLI' must match the name in Global Tool Configuration.
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
            // New Groovy script block to resolve the scanner tool path
            steps {
                echo 'Starting Static Analysis with SonarQube...'
                
                // --- Groovy Script to get the Tool Path ---
                script {
                    // 1. Get the path to the installed tool named 'SonarScannerCLI'
                    // The tool() function returns the full installation directory path.
                    def sonarScannerHome = tool 'SonarScannerCLI'
                    
                    // 2. Use the path inside the withSonarQubeEnv block
                    withSonarQubeEnv('lab12') { 
                        // We construct the full command path: <tool_home>\bin\sonar-scanner.bat
                        def scannerCommand = "${sonarScannerHome}\\bin\\sonar-scanner.bat"
                        
                        // Execute the command using the full path
                        // IMPORTANT: Project Key must match your SonarQube project
                        bat "\"${scannerCommand}\" -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=." 
                    }
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
