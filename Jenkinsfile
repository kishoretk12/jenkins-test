pipeline {
    agent any

    tools {
        maven 'Maven' // Name of the Maven installation in Jenkins
        jdk 'Java' // Name of the JDK installation in Jenkins
    }

    environment {
        SONAR_PROJECT_KEY = 'com.example:demo-project'
        SONAR_HOST_URL = 'http://your-sonarqube-server'
        SONAR_LOGIN_KEY = 'your-sonarqube-authentication-token'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm // This assumes Jenkins is configured with SCM details
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('Your SonarQube Server') {
                        sh """
                            mvn sonar:sonar \
                                -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
                                -Dsonar.host.url=${env.SONAR_HOST_URL} \
                                -Dsonar.login=${env.SONAR_LOGIN_KEY}
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            echo 'Quality Gate Failed: ${qg.status}'
                            error "Pipeline aborted due to quality gate failure"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up'
            sh 'mvn clean'
        }
        success {
            echo 'Build and analysis completed successfully.'
        }
        failure {
            echo 'Build or analysis failed.'
        }
    }
}
