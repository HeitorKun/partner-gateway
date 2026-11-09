// Declarative pipeline: build and test, Sonar analysis + quality gate, then the image on main.
// Needs a Docker-capable agent (Testcontainers) and a SonarQube server named 'sonarqube' in Jenkins.
pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
    }

    environment {
        IMAGE = "partner-gateway:${env.GIT_COMMIT?.take(7) ?: 'local'}"
    }

    stages {
        stage('Build and test') {
            steps {
                sh './mvnw -B verify'
            }
            post {
                always {
                    junit 'target/surefire-reports/junitreports/*.xml'
                    archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
                }
            }
        }

        stage('Sonar analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh './mvnw -B sonar:sonar -Dsonar.projectKey=partner-gateway'
                }
            }
        }

        stage('Quality gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker image') {
            when { branch 'main' }
            steps {
                sh 'docker build -t "$IMAGE" .'
            }
        }
    }
}
