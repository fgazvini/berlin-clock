pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
        stage('install and sonar parallel') {
            steps {
                parallel(install: {
                    sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                }, sonar: {
                    sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST} -Dsonar.login=b295bf6cea81410cb8c6fad74d9227ad2e75f2ad"
                })
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
            }
        }
        stage('Sonar') {
            steps {
                sh "echo 'Sonar step is parallelized with cobertura step in previous step'"
//              sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST} -Dsonar.login=b295bf6cea81410cb8c6fad74d9227ad2e75f2ad"
            }
        }
        stage('Deploy on Artifactory') {
            steps {
                sh "mvn deploy -DskipTests"
            }
        }
    }
}
