pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS') // Set the timeout for the entire pipeline run
    }

    tools {
        maven 'maven'
        jdk 'java'
    }

    environment {
        SONAR_PROJECT_KEY = 'com.example:demo-project'
        SONAR_HOST_URL = 'http://54.183.16.150:9000/'
        SONAR_LOGIN_KEY = 'squ_00e59a483d0dd795a8202ff6a549baf1b2bcdffe'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kishoretk12/jenkins-test.git'
            }
        }

        stage('Build with Maven') {
            options {
                timeout(time: 30, unit: 'MINUTES') // Set a timeout for this specific stage
            }
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            options {
                timeout(time: 30, unit: 'MINUTES') // Set a timeout for this specific stage
            }
            steps {
                script {
                    withSonarQubeEnv('sonar-server') {
                        sh """
                            mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=com.example:demo-project \
                            -Dsonar.host.url=http://54.183.16.150:9000 \
                            -Dsonar.login=squ_00e59a483d0dd795a8202ff6a549baf1b2bcdffe
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
        }

        stage('Next Build') {
            when {
                expression { currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Starting next build...'
                // Add your specific build steps here
            }
        }
    }

    post {
        always {
            echo 'Cleaning up'
            sh 'mvn clean'
        }
    }
}
