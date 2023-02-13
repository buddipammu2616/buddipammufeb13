pipeline{
    
    agent any
    environment {
        PATH = "/opt/maven3.6/bin/:$PATH"
    }
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'master', url: 'https://github.com/buddipammu2616/buddipammufeb13.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('build code') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('sonarqibe analysis') {
            steps {
                script {
                   withSonarQubeEnv(credentialsId: 'sonar-key') {
                     sh 'mvn clean package sonar:sonar'
} 
    }
            }
        }
        stage('quality gate check') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-key'
                }
            }
        }
        stage('war file upload') {
            steps {
                script {
                    def readpomversion = readMavenPom file: 'pom.xml'
                    def nexusrepo = readpomversion.version.endsWith("SNAPSHOT") ? "helloworldapp-snapshot" : "helloworldapp-release"
                    nexusArtifactUploader artifacts: 
                        [ 
                        [
                            artifactId: 'buddipammu', 
                            classifier: '', 
                            file: 'target/buddipammu.war', 
                            type: 'war'
                        ]
                    ], 
                        credentialsId: 'nexus-key', 
                        groupId: 'com.ustglobal', 
                        nexusUrl: '52.66.247.151:8081', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: nexusrepo, 
                        version: "${readpomversion.version}"
                }
            }
        }
        stage('tomcat deployment') {
            steps {
                script {
                    deploy adapters: [tomcat8(credentialsId: 'tomcat-credentials', path: '', url: 'http://13.126.182.173:2020/')], contextPath: null, war: '**/*.war'
                }
            }
        }
    }
}

