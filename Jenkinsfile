pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        SONARQUBE_ENV = 'MySonarQube'   // name configured under Manage Jenkins → System → SonarQube Servers
        PROJECT_KEY = 'simple-java-maven-app' // must match your SonarQube project key
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📥 Checking out source code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🏗️ Building project with Maven...'
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo '🔍 Running SonarQube code analysis...'
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=${PROJECT_KEY}'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo '🚦 Checking SonarQube quality gate status...'
                script {
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build, test, and SonarQube analysis completed successfully!'
        }
        failure {
            echo '❌ Build failed or SonarQube quality gate not passed.'
        }
    }
}
