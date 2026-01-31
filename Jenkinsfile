pipeline {
    agent any

    tools {
        // This must match the name you gave Maven in "Global Tool Configuration"
        maven "Maven3" 
    }

    stages {
        stage('Checkout') {
            steps {
                // Jenkins fetches the code from GitHub
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                // Navigate to the app folder and create the .jar file
                dir('app/sample-app') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                dir('app/sample-app') {
                    sh 'mvn test'
                }
            }
        }stage('SonarQube Analysis') {
            steps {
                // 'SonarQube-Server' must match the name in "Manage Jenkins > System"
                withSonarQubeEnv('SonarQube-Server') {
                    dir('app/sample-app') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'Build Process Completed.'
        }
    }
}
