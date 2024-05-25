pipeline {
    agent any 
    tools {
        maven 'mymaven'
    }
    stages {
        stage ('CloneRepo') {
            steps {
                echo 'This is stage 1'
                git 'https://github.com/puneetbhatia77/addressbook.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Code Review') {
            steps {
                sh 'mvn -f addressbook_main/pom.xml pmd:pmd'
            }
            post {
                success {
                    recordIssues sourceCodeRetention: 'LAST_BUILD', tools: [pmdParser(pattern: '**/pmd.xml')]
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                success {
                     junit stdioRetention: '', testResults: 'addressbook_main/target/surefire-reports/*.xml'
                }
            }
        }
         stage('QAMetricCheck') {
            steps {
                script {
                    // Set JAVA_HOME if necessary
                    env.JAVA_HOME = '/usr/lib/jvm/java-1.8.0-openjdk-amd64'
                    sh 'echo $JAVA_HOME'
                    sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'                   
               }         
            }
             post {
                success {
                     cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
                }
            }
         }
        stage('package') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
