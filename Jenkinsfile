pipeline {
    agent any

    triggers {
        // Cron: every 5 minutes, only on Mondays (1 = Monday in Jenkins cron)
        cron('H/5 * * * 1')
    }

    tools {
        maven 'MAVEN3'
        jdk 'JDK21'     
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test with JaCoCo') {
            steps {
                sh 'mvn clean verify'
                // 'verify' runs tests AND the jacoco:report goal bound to the verify phase
            }
        }

        stage('Publish JaCoCo Coverage Report') {
            steps {
                jacoco(
                    execPattern:   '**/target/jacoco.exec',
                    classPattern:  '**/target/classes',
                    sourcePattern: '**/src/main/java',
                    exclusionPattern: '**/src/test*'
                )
            }
        }

        stage('Archive Artifact') {
            steps {
                // Archives the built JAR as a Jenkins artifact
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            // Publish JUnit test results
            junit '**/target/surefire-reports/*.xml'
        }
        success {
            echo 'Pipeline completed successfully. Artifact archived.'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}