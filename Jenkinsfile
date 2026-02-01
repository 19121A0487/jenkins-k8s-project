pipeline {
    agent any

    tools {
        // These must match your "Global Tool Configuration" names
        maven "Maven3"
        jdk "Java17"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                dir('app/sample-app') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                dir('app/sample-app') {
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Ensure 'SonarQube-Server' matches your Jenkins System settings
                withSonarQubeEnv('SonarQube-Server') {
                    dir('app/sample-app') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                dir('app/sample-app') {
                    script {
                        // 'dockerhub-creds' must be the ID of your credentials in Jenkins
                        docker.withRegistry('', 'dockerhub-creds') {
                            def customImage = docker.build("sreepathi0208/jenkins-project:${env.BUILD_ID}")
                            customImage.push()
                            customImage.push('latest')
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Build Process Completed.'
        }
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
