pipeline {
    agent any

    tools {
        maven 'maven'
    }
    
    stages {
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/olufemiige/web-app.git'
            }
        }
        
        stage('maven build'){
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('sonarqube code analysis'){
            environment {
                ScannerHome = tool 'sonar'
            }
            steps{
               script{
                   withSonarQubeEnv('sonar'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=john-webapp"
                   }
               }
           }
        }
        
        //stage("Quality Gate") {
            //steps {
              //timeout(time: 30, unit: 'SECONDS') {
                //waitForQualityGate abortPipeline: false
              //}
            //}
        //}
        
        stage('uploads to nexus'){
           steps{
             nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', 
             classifier: '', 
             file: '/var/lib/jenkins/workspace/jomacs-webapp-jenkinsfile/target/web-app.war', 
             type: 'war']], 
             credentialsId: 'nexus-credentials', 
             groupId: 'com.mt', 
             nexusUrl: '18.207.195.153:8081/repository/jomacs-webapp/', 
             nexusVersion: 'nexus3', 
             protocol: 'http', 
             repository: 'jomacs-webapp', 
             version: '3.0.6-RELEASE'  
           }
           
       }
       
       stage('deploy to prod'){
           steps{
               deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://98.82.199.109:8080/')], 
               contextPath: null, 
               war: 'target/web-app.war'
           }
       }
       
        
        
    }
    
    post {
        success {
            slackSend channel: 'devopscicd', color: 'good', message: "Build successful: ${currentBuild.fullDisplayName}"
        }
        failure {
            slackSend channel: 'devopscicd', color: 'danger', message: "Build failed: ${currentBuild.fullDisplayName}"
        }
        aborted {
            slackSend channel: 'devopscicd', color: 'warning', message: "Build aborted: ${currentBuild.fullDisplayName}"
        }
    }
    
}