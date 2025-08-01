pipeline {
    agent any

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        MAVEN_HOME = "/opt/apache-maven-3.9.11"
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/abilashnimmala/samplejavaapp.git', branch: 'master'
            }
        }

        stage('Unit Test') {
            steps {
                echo "🧪 Running Unit Tests..."
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Regression Test') {
            steps {
                echo "🔄 Running Regression Tests..."
                // Add your regression test commands here
                // Example: sh 'mvn verify -P regression' if you have a regression profile
                sh 'mvn verify'  
            }
        }

        stage('Static Code Analysis - PMD') {
            steps {
                echo "🔍 Running PMD for code analysis..."
                sh 'mvn pmd:pmd'
            }
            post {
                always {
                    recordIssues tools: [pmdParser(pattern: 'target/pmd.xml')]
                }
            }
        }

        stage('Code Coverage') {
            steps {
                echo "📊 Running Code Coverage using JaCoCo..."
                sh 'mvn jacoco:prepare-agent test jacoco:report'
            }
            post {
                always {
                    jacoco execPattern: 'target/jacoco.exec'
                }
            }
        }

        stage('Package') {
            steps {
                echo "📦 Packaging the application..."
                sh 'mvn package'
            }
        }

        stage('Copy Artifact to Jenkins Server') {
            steps {
                echo "📋 Copying built package to Jenkins workspace..."
                // Adjust the jar name/path below as necessary
                sh 'cp target/*.jar $WORKSPACE'
            }
        }
    }
  
    post {
        success {
            echo '✅ Build, test, and package completed successfully!'
        }
        failure {
            echo '❌ Build failed. Check logs for details.'
        }
    }
}

