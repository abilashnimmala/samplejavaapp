pipeline {
    agent any
    tools {
        jdk 'Java 17'
        maven 'Maven-3.9.3'
    }
    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        M2_HOME = '/opt/apache-maven-3.9.3'
    }
    options {
        timestamps()
        // ansiColor('xterm')
    }
    stages {
        stage('Verify Java and Maven') {
            steps {
                echo 'Checking Java and Maven versions on Jenkins agent...'
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   java -version
                   mvn -version
                   echo JAVA_HOME=$JAVA_HOME
                   echo M2_HOME=$M2_HOME
                '''
            }
        }
        stage('Compile') {
            steps {
                echo 'Compiling...'
                git url: 'https://github.com/abilashnimmala/samplejavaapp.git'
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   mvn compile
                '''
            }
        }
        stage('Code Review - PMD') {
            steps {
                echo 'Running PMD code review...'
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   mvn -P metrics pmd:pmd
                '''
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
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   mvn test
                '''
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
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   mvn verify
                '''
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
                sh '''
                   export JAVA_HOME=${JAVA_HOME}
                   export PATH=${JAVA_HOME}/bin:${M2_HOME}/bin:$PATH
                   mvn package
                '''
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
