pipeline {
    agent any

    tools {
        // Ensure these match the names in "Manage Jenkins > Tools"
        maven "Maven3"
        jdk "Java17"
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls code from your GitHub repository
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                dir('app/sample-app') {
                    // Build the JAR file, skipping tests here as we have a separate stage
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                dir('app/sample-app') {
                    // Run Maven unit tests
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // This 'SonarQube-Server' name must match your Jenkins System configuration
                withSonarQubeEnv('SonarQube-Server') {
                    dir('app/sample-app') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                dir('app/sample-app') {
                    // Uses 'nexus-creds' from Jenkins Credentials (Username/Password)
                    withCredentials([usernamePassword(credentialsId: 'nexus-creds', passwordVariable: 'NEXUS_PWD', usernameVariable: 'NEXUS_USER')]) {
                        sh 'mvn deploy -DskipTests'
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                dir('app/sample-app') {
                    script {
                        // Uses 'dockerhub-creds' (Docker Hub Username/Access Token)
                        docker.withRegistry('', 'dockerhub-creds') {
                            // Builds and tags with the build number and 'latest'
                            def customImage = docker.build("sreepathi0208/jenkins-project:${env.BUILD_ID}")
                            customImage.push()
                            customImage.push('latest')
                        }
                    }
                }
            }
        }

        stage('Trivy Security Scan') {
            steps {
                script {
                    // Scans the newly pushed image for High and Critical vulnerabilities
                    // --exit-code 0 ensures the build continues even if issues are found
                    sh 'trivy image --severity HIGH,CRITICAL sreepathi0208/jenkins-project:latest'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline Execution Finished.'
        }
        success {
            echo '🎉 Deployment Ready! Artifacts in Nexus and Image in Docker Hub.'
        }
        failure {
            echo '❌ Pipeline Failed. Please check the logs above.'
        }
    }
}
