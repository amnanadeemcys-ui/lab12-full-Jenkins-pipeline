pipeline {
    agent any
    
    // 1. Specify the SonarScanner tool configured in Jenkins
    tools {
        sonarScanner 'SonarScannerCLI' 
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                // ... existing build steps ...
            }
        }
        
        // --- NEW SAST STAGE ---
        stage('SonarQube Analysis (SAST)') {
            steps {
                echo 'Starting Static Analysis with SonarQube...'
                
                // This connects to the server named 'MySonarQubeServer'
                withSonarQubeEnv('MySonarQubeServer') { 
                    // Use 'bat' for Windows batch commands
                    // The command executes the scan
                    bat "sonar-scanner -Dsonar.projectKey=my-devsecops-project -Dsonar.sources=." 
                }
                
                // Pause Pipeline and Check Quality Gate (CRUCIAL SECURITY STEP)
                script {
                    timeout(time: 5, unit: 'MINUTES') { 
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "SAST Failed! Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
                // This stage will only run if SAST Quality Gate is 'OK'
            }
        }
        
        // ... rest of your stages ...
    }
}
