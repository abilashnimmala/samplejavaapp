pipeline {
    agent any
    tools {
        jdk 'Java 17'          // Exact name from Jenkins Global Tool Configuration
        maven 'Maven-3.9.3'    // Exact name from Jenkins Global Tool Configuration
    }
    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        M2_HOME = '/opt/apache-maven-3.9.3'
        PATH = "${JAVA_HOME}/bin:${M2_HOME}/bin:${env.PATH}"
    }
    options {
        timestamps()
        // ansiColor('xterm')  // Removed/commented out due to plugin/version incompatibility
    }
    stages {
        stage('Verify Java and Maven') {
            steps {
                echo 'Checking Java and Maven versions on Jenkins agent...'
                sh 'java -version'
                sh 'mvn -version'
                sh 'echo JAVA_HOME=$JAVA_HOME'
                sh 'echo M2_HOME=$M2_HOME'
            }
        }
        stage('Compile') {
            steps {
                echo 'Compiling...'
                git url: 'https://github.com/abilashnimmala/samplejavaapp.git'
                sh 'mvn compile'
            }
        }
        stage('Code Review - PMD') {
            steps {
                echo 'Running PMD code review...'
                sh 'mvn -P metrics pmd:pmd'
            }
            post {
                success {
                    recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
                }
            }
        }
        stage('Unit Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
            post {
                success {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Code Coverage') {
            steps {
                echo 'Running code coverage analysis...'
                sh 'mvn verify'
            }
            post {
                success {
                    jacoco buildOverBuild: true, deltaBranchCoverage: '20', deltaClassCoverage: '20', deltaComplexityCoverage: '20', deltaInstructionCoverage: '20', deltaLineCoverage: '20', deltaMethodCoverage: '20'
                }
            }
        }
        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh 'mvn package'
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
